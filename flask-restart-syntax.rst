:Title: How to get Flask to auto-restart despite syntax errors
:Date: 2019-01-27
:Tags: Python
:Category: Python

When developing a Flask application, the auto-reloader is incredibly helpful. Each time I edit a python file the server will restart, so that 
my changes are immediately implemented. However, there is still room for improvement, which is what I will briefly discuss and fix in this post.

First, for context, I will explain how I typically set up a flask app for development and deployment.

Setting Up
----------

Typically, in the root of the folder which holds all of the files, I will have a file named "run.py". This will contain the necessary code for 
running the development server (provided by the Flask library itself) if launched by itself, or providing the necessary objects to uwsgi if used 
in production. 

Improving Flask's default server
--------------------------------

Templates, JS and CSS
=====================

When working on any front-end aspects of a flask website the auto reloader will not kick in to update any template or static file changes. 

Luckily, there is a way to include extra files with the reloader, which helps with this problem. Specifically, there is an ``extra_files`` argument 
which can be passed to ``app.run()`` so that it will watch additional, non-.py files. 

Making use of this, we can update ``run.py`` to contain the following:

.. code-block:: python

    import os

    from os import path
    from app_root_module import app as application

    if __name__ == '__main__':
        extra_dirs = ['/path/to/templates', "/path/to/static/js", "/path/to/static/css"]
            extra_files = extra_dirs[:]
            for extra_dir in extra_dirs:
                for dirname, dirs, files in os.walk(extra_dir):
                    for filename in files:
                        filename = path.join(dirname, filename)
                        if path.isfile(filename):
                            extra_files.append(filename)

        application.run(debug=True, extra_files=extra_files) 


Now Flask will restart its server whenever we change a front-end file. Awesome!

(Code was taken from `Stackoverflow <https://stackoverflow.com/questions/9508667/reload-flask-app-when-template-file-changes>`_).

Handling Syntax Errors
======================

The server will completely stop when it encounters a syntax error while running. This is especially problematic if you use an IDE which auto-saves 
after you stop typing for a couple of seconds. If you stop half way through a line, the IDE will save, the server will restart, and then crash with 
a syntax error. Very annoying!

Luckily, a couple of unix tools can make this a non-issue. Introducing ``entr``  and ``ack`` (not to be confused with ``awk``). I have neither 
installed by default on OpenSUSE Tumbleweed, but both were available in Zypper.

Using these two tools, we can craft a single command which will restart our server once we adjust a python file, regardless of any syntax errors 
occurring. 

I have the following as an executable bash file, which I run to start my dev sever:

``ack -g ".py" --ignore-dir "env" --ignore-dir "htmlcov" | entr -r python3 run.py``

This uses ``ack`` to recursively list all files ending in ``.py``, except those in my virtual env or coverage directory, then pipe them through to 
``entr`` which will execute a command when any of them change. In this case, the command executes the ``run.py`` file, starting Flask's web server 
again.

That's all there is to it. I don't claim to be very knowledgeable about either ``ack`` or ``entr``, so I will leave links to their respecive 
documentation below.

`Read about entr here <http://eradman.com/entrproject/>`_

`Read about ack here <https://linux.die.net/man/1/ack>`_
