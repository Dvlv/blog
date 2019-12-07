:Title: A Super Helpful Decorator For Peewee/Flask Unit Testing
:Date: 2018-06-22
:Category: Python
:Tags: Python, Testing, Web


Update
======

As of peewee version 3, the ``test_database`` context manager was removed, and models are instead bound to a new database via a ``bind`` method.

There is a comment by the author of Peewee `over on a github issue <https://github.com/coleifer/peewee/issues/1450>`_ providing a new way to achieve this.

I have adapted this to look similar to the decorator I am used to.

.. code-block:: python

    def with_test_db(dbs: tuple):
        def decorator(func):
            @wraps(func)
            def test_db_closure(*args, **kwargs):
                test_db = SqliteExtDatabase(":memory:")
                with test_db.bind_ctx(dbs):
                    test_db.create_tables(dbs)
                    try:
                        func(*args, **kwargs)
                    finally:
                        test_db.drop_tables(dbs)
                        test_db.close()

            return test_db_closure

        return decorator 

Replace the decorator function in the original article with this new one if you are following along with Peewee 3. Everything else will work the same.

Original Post
=============


When writing unit tests for a Flask/Peewee application, we will inevitably need to mock the database in order to test methods of our model classes.

Playhouse defines a very easy and pythonic way to achieve this - a ``test_database`` context manager. We can just wrap this around our test method to have it use an in-memory database instead of our actual one.

This looks like so:

.. code-block:: python

    def test_model_method():
        test_database = SqliteExtDatabase(":memory:")
        with test_database(test_database, (MyModel,)):
            x = MyModel().some_method()
            assert x == "test"

However, defining our database and using the context manager around every test gets tedious. For this reason I have come up with a decorator which will help to tidy up any code which needs to access the mock database.

For this code snippet, we will need some imports:

    - ``test_database`` - the context manager which handles a testing instance of a peewee database
    - ``wraps`` - for help with creating a decorator
    - ``SqliteExtDatabase`` - for using some later features of SQLite
    - ``JSONField`` - for JSON compatability

.. code-block:: python

    from functools import wraps
    from playhouse.sqlite_ext import JSONField, SqliteExtDatabase
    from playhouse.test_utils import test_database

We will use the ``wraps`` decorator to create a decorator of our own. This will be responsible for creating the test database and wrapping the ``test_database`` context manager around our unit test.

.. code-block:: python

    def with_test_db(dbs):

        def decorator(func):
            @wraps(func)
            def test_db_closure(*args, **kwargs):
                test_db = SqliteExtDatabase(":memory:")
                with test_database(test_db, tuple(dbs), fail_silently=True):
                    func(*args, **kwargs)

            return test_db_closure

        return decorator

Since most of our Python apps run with Postgres, I use the ``SqliteExtDatabase`` to mock JSON storage. If you are not using JSON, feel free to use a regular ``SqliteDatabase``.

With this done and imported, just use it in your unit test files like so:

.. code-block:: python

    @with_test_db((MyModel,))
    def test_model_method():
        x = MyModel().some_method()
        assert x == "test"



