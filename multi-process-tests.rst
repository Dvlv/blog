:Title: Running Flask Tests In Parallell with Pytest
:Date: 2019-12-07
:Category: Python
:Tags: Python, Testing, Web

I like having high test coverage when building websites at work, but the more tests I write, the slower the suite becomes to run. This not 
only could discourage other team members from running the test suites themselves, but it also increases the time it takes for CI/CD to 
deploy changes after we push them live.

To speed this up there are pytest plugins like ``pytest-xdist`` and ``pytest-parallell`` to run multiple tests concurrently. In the context of 
a flask application with a database connection, however, these are not going to work straight away.

To illustrate the problem, let's look at a hypothetical integration test: 


.. code-block:: python

    @with_test_db((User, UserRole,))
    def test_create_user_post_creates_user_and_assigns_default_role():
        assert User.select().count() == 0
        assert UserRole.select().count() == 0

        user_data = {...}

        response = testing_app.post("/users/create", data=user_data)

        assert response.status_code == 200
        assert User.select().count() == 1
        assert UserRole.select().count() == 1
        assert UserRole.get().name == "basic"


(You can read about the `with_test_db decorator in my blog post here <https://www.dvlv.co.uk/a-super-helpful-decorator-for-peeweeflask-unit-testing.html>`_ )
 
The above test would work no problem when run against a clean database, but each assert could fail if another process has updated the 
database before this test has had a chance to fully run. This was a blocker for a while in my attempts to speed up the test suites 
for a few flask projects.

This week I managed to get around it, and the tests now take around 1/3rd the amount of time to run.

The initial set-up
==================
Python projects at work use Flask as the web framework and Peewee as the database ORM.

Each developer had postgres set up on either their laptop or a VM. For each project which ran unit tests they would be required to create a 
user called ``<project_name>_test`` and an empty database with the same name, and grant that user all privileges on the database.

Between each test, this database has the necessary tables created and dropped by a context manager provided by Peewee. This is provided by 
the ``with_test_db`` decorator defined in a file which lives at ``tests/helpers.py``. 


The Solution
============

Each process created needs its own database which it can populate and tear down between each individual test. This required finding a way to 
get pytest to run some code at the beginning and end of the entire test suite, separately per process.

Luckily this is very trivial, and is done using a ``conftest.py`` file in the root of the project directory.

Inside this file, we need to make a `pytest fixture <https://docs.pytest.org/en/2.8.7/fixture.html>`_ which will run at the "session" scope, and 
be automatically used by each test.

The set-up fixture needs to create a randomly-named database for its process, and pass this along to ``with_test_db`` so that it can be used to 
run all tests assigned to this particular process, and cleaned in between.

The tear-down step of the fixture just needs to drop this database, since we don't want them hanging around.

Here's a full dump of a ``conftest.py`` file from one of my projects, with the project name removed:

.. code-block:: python

    import logging
    import os
    import random
    import time

    import psycopg2
    from psycopg2.sql import SQL
    import pytest

    from tests import helpers as test_helpers

    DB_HOST = test_helpers.DB_HOST

    logging.basicConfig(format='%(message)s')


    def get_cursor():
        """
        Gets a psycopg2 cursor for the parent Database
        """
        conn = psycopg2.connect(
            dbname="project_test",
            user="project_test",
            password="project_test",
            host=DB_HOST
        )

        conn.set_isolation_level(0)

        return conn.cursor()


    def create_database(db_name: str):
        cur = get_cursor()

        cur.execute(
            SQL(
                f"create database {db_name};"
            )
        )
        cur.execute(
            SQL(
                f"grant all privileges on database {db_name} to project_test;"
            )
        )


    def drop_database(db_name: str):
        cur = get_cursor()

        cur.execute(
            SQL(
                f"drop database {db_name};"
            )
        )


    def create_random_db():
        time_str = "".join(str(time.time()).split("."))
        random.seed()
        pref = random.randint(1111, 9999)

        random_db = "project_test_" + "_".join([time_str, str(pref)])
        create_database(random_db)

        return random_db


    @pytest.fixture(scope="session", autouse=True)
    def use_random_db(request):
        """
        Forces each parallell worker to generate and use their own random DB.
        This is the key to letting us test in parallell!
        """
        rand_db = create_random_db()
        test_helpers.random_db_name = rand_db
        logging.warning("\n creating db " + str(rand_db) + "\n")

        def after_all_worker_tests():
            logging.warning("\n dropping db " + str(rand_db) + "\n")
            drop_database(rand_db)

        request.addfinalizer(after_all_worker_tests)


Here we define a fixture called ``use_random_db`` which creates a long, randomly-named database via ``psycopg2`` and logs a message to the 
console letting us know. This random name is then passed to the ``tests/helpers`` module for use in ``with_test_db``.

The ``after_all_worker_tests`` teardown is added as a finalizer, which will log that it is removing the database and then do so using ``psycopg2``.

Hopefully this is all self-explanitory. 

Once I had figured that out, I had to make some small changes to my ``tests/helpers.py`` file to accommodate for using the random database:


.. code-block:: python

    random_db_name = ""  # "global" variable set by conftest.py

    def with_test_db(dbs: tuple):
        def decorator(func):
            @wraps(func)
            def test_db_closure(*args, **kwargs):
                db_name = random_db_name or "project_test"

                test_db = PostgresqlDatabase(
                    db_name,
                    user="project_test",
                    password="project_test",
                    host=DB_HOST,
                )
                with test_db.bind_ctx(dbs):
                    test_db.create_tables(dbs)
                    try:
                        func(*args, **kwargs)
                    finally:
                        test_db.drop_tables(dbs)
                        test_db.close()

            return test_db_closure

        return decorator


This decorator will now use the module-level variable ``random_db_name`` set by ``conftest.py`` to pull in the name of this particular process' 
database. Since this module exists separately in each process created by ``pytest-xdist`` the random names will be different in each one.

Note that I am not super-keen on the cross-module variable ``random_db_name``, and if ``tests/helpers`` wasn't already fully woven into my test 
files I would probably define ``with_test_db`` inside ``conftest.py``. 

With that out of the way, we can use ``pytest-xdist`` to run our tests in multiple processes! 

I made some comparisons over three projects which were built on this stack, and the results are as follows:

Project 1 - 139 tests
---------------------

Average in serial: ~15 seconds

Average with 4 workers: ~4.5 seconds

Project 2 - 199 tests
---------------------

Average in serial: ~20 seconds

Average with 4 workers: ~7 seconds

Project 3 - 316 tests
---------------------

Average in serial: ~53 seconds

Average with 4 workers: ~15.5 seconds


As can be seen above, each suite now takes about 1/3rd as long to run as it used to. I consider this a significant improvement!
