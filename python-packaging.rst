:Title: I Can't Find a Python Package Manager I Like
:Date: 2020-07-04
:Tags: Python
:Category: Python

As I make more and more projects at work using our typical flask / peewee / uwsgi / nginx stack, I end up repeating a lot of the more boilerplate-y stuff.

One of these things is managing dependencies amongst multiple environments, namely development, CI / CD, and production.

Obviously python comes with ``pip`` to handle this, and it *works*, but it doesn't fully meet my desires. 

I'll start off by explaining the problems I have, what my ideal package manager would do, and then go over why each option I've found doesn't seem to quite meet all of its features.

Problems
========

* Unnecessary packages are build every time we run CI/CD (main culprit is ``uwsgi`` which takes quite some time to install).
* Uncecessary packages are installed to live (e.g. ``pytest``).
* Packages experimented with can easily end up in our live dependencies.

My Ideal Package Manager
=========================

* Seperate, named environments (arbitrarily).
* Ability to include or exclude a package on a particular named environment.
* Reproducible builds.
* Clean CLI interface.
* Easy ability to include local wheels.

Upon writing this, it's become clearer to me that my requirements aren't actually as much as I'd thought. I think the first and second are the main two which aren't currently met however.

With that listed, I'll now go over various solutions I've found and say where they do and don't meet my needs.


Pip
===

All python projects at my workplace are using the standard pip workflow to manage dependencies at the moment.

Pip obviously meets the reproducible builds requirement via ``pip install -r requirements.txt``, and has a nice CLI interface.

It seems to be the only tool which allows easy use of local wheels, which is a crucial part of keeping our deploy times as low as possible.

Separate named environments is where pip falls down. Technically I could create ``requirements.txt``, ``requirements-dev.txt`` and ``requirments-ci.txt`` manually, and get people
to install these manually when being introduced to the project, but this is far from optimal. What's a bigger problem with this approach is updating requirements as time goes on.

When we add features which require new dependencies, we typically just use ``pip freeze > requirements.txt`` to add these to the requirements list. This is already problematic
as it means if we install a dependency via ``pip install`` but never actually add it to the codebase, this dependency will make it into the requirements file unless we
rememeber to uninstall it from our local virtualenv before the next ``pip freeze``.

I *could* try to convince people to install new dependencies by manually adding a line to ``requirements.txt`` then using ``pip install -r requirements.txt`` to install it,
but then we would lose the version locking (not to mention it's a bit more long-winded than a single cli command).


Pipenv
======

`Pipenv <https://pypi.org/project/pipenv/>`_ doesn't seem to solve many of the problems with ``pip`` aside from the introduction of allowing separate dev dependencies. This is nice, but I still need CI-only dependencies too,
as well as a way to exclude in some way.

Pipenv is also quite slow, and needs a particular command to install ``black``, which we use for our formatting standards, because there currently only exists beta-level versions.

Poetry
======

I haven't given `poetry <https://python-poetry.org/>`_ as much time as Pipenv because it was buggy when I tried it out. I can't say I'm a huge fan of the ``pyproject.toml`` thing, but that might
be because I don't distribute packages. 

I still don't see a way to have arbitrary requirement categories to let me separate out dev, ci and production requirements.

Honorable Mentions
==================

I also checked out `dephell <https://github.com/dephell/dephell>`_ and `pyflow <https://pypi.org/project/pyflow/>`_ and neither of those seemed to solve anything new. 

My (Partial) Solution
=====================

The project I found with the most promise was `pip-tools <https://github.com/jazzband/pip-tools>`_. This awkwardly splits management into two separate commands, ``pip-compile`` and ``pip-sync``, but it made splitting
dependencies into arbitrary groups a bit smoother.

What ``pip-tools`` lacks is the simple command line interface, but that's a gap I can fill myself. I've created a command line tool, creatively called `pip-tools-helper <https://github.com/Dvlv/pip-tools-helper>`_
which now allows me to add requirements to arbitrary environments. 

The whole using-local-wheels thing becomes a non-issue, since ``pip-tools`` functions so much like the normal ``pip`` that I can keep the old ``pip`` command in our ansible
scripts to use local wheel files.

``pip-tools`` also doesn't try to reinvent virtualenvs, so all of our behaviour in that area can also stay the same.

Now, to try out a package without having to remember to remove it from the requirements, we can just source the virtualenv and use regular ``pip install`` to play around wih it.

If we decide to keep the requirement, it can be added to the persistence files with ``pth install``.

The one problem I have not managed to solve is the removal of packages on a particular install. Namely, since ``uwsgi`` is in our production ``requirements.txt`` it will keep
installing on our CI containers. I assume there could be some way of handling this without having to make the CI requirements contain almost all of the production requirements
as well as CI-only ones, but I am yet to come up with a solution which does not involve running some form of ``sed /uwsgi/d`` in our CI script.

For now though, this is the best I have come up with. It's only in use for projects I manage alone, so I am yet to test it out on co-workers. 
