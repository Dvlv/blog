:Title: Watching files for changes in python with Watchdog
:Date: 2020-08-15
:Category: Python
:Tags: Python

As a web developer, I like to use less and typescript to make css / javascript a little nicer to work with. Even nicer still is to have a process
watch these files for changes, then automatically compile them on save, to make development as streamlined as if you were using css or javascript itself.

However, I prefer to stay as far away from the world of ``npm`` as possible.

The standard way of watching and compiling both of these things is by downloading packages with ``npm`` and running some kind of ``npm run watch`` command.

However, since my web projects are all using python for both the front and back end, I'd prefer to leverage that for as much of the development
process as I can.

Enter Watchdog
==============

Watchdog is a python library which lets you watch over a directory and react to changes in the files. I'm sure it does more than that, but this
is the only feature I use.

Watchdog has a nice feature called "tricks" which has us create a ``tricks.yaml`` file as well as a ``Trick`` class to make the setup fairly painless.

My Tricks.yaml
==============

.. code-block:: python

    tricks:
    - helpers.watcher.watcher.LessCompileTrick:
        src_dir: 'web/admin/static/less'
        dest_dir: 'web/admin/static/css'
        patterns: ['*.less']

    - helpers.watcher.watcher.TsCompileTrick:
        src_dir: 'web/admin/static/ts'
        dest_dir: 'web/admin/static/js'
        patterns: ['*.ts']

This file defines a trick per compilation. Both tricks specify the source and destination directories of the languages, and the file types to watch.

My tricks classes
=================

.. code-block:: python

    import os

    from watchdog.tricks import Trick


    class LessCompileTrick(Trick):
        def __init__(self, src_dir, dest_dir, **kwargs):
            self.src_dir = src_dir
            self.dest_dir = dest_dir
            super().__init__(**kwargs)

        def on_modified(self, event):
            self.compile()

        def compile(self):
            """
            Compiles less into css

            :return: ``None``
            """
            cmd = "lesscpy -r " + self.src_dir + " -o" + self.dest_dir
            os.system(cmd)


    class TsCompileTrick(Trick):
        def __init__(self, src_dir, dest_dir, **kwargs):
            self.src_dir = src_dir
            self.dest_dir = dest_dir
            super().__init__(**kwargs)

        def on_modified(self, event):
            self.compile()

        def compile(self):
            """
            Compiles ts into js

            :return: ``None``
            """
            os.system("tsc --inlineSourceMap")
     


As indicated by the ``tricks.yaml`` file, this code lives at ``helpers/watcher/watcher.py`` and defines two subclasses of the provided ``Trick`` class.

Both define a special ``on_modified`` method, which is called when a file is changed. In this simpler example, both classes then just call a single
other method called "compile" which uses ``os.system`` to call a shell command to compile the entire watched directory.

For more complex requirements, the ``event`` parameter available to ``on_modified`` contains details about the exact file changed, but luckily
for me both the less and typescript compilers can run over a whole directory.

Running
=======

With these files in place, when working on a project I can open a terminal tab, ``cd`` to the project's root directory and run ``watchmedo tricks tricks.yaml``
to have python code watch for changes to either ``.less`` files or ``.ts`` files, and it will automatically compile them for me.
