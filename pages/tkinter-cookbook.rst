:Title: The Tkinter Cookbook
:Date: 2018-06-30

This post will help with small, basic questions you may have about how to do a specific thing in tkinter with python.

If you want an in-depth breakdown of creating useful apps with tkinter check out my free book - Tkinter by Example:

`Get Tkinter By Example on Github
<http://www.python.org/>`_

Creating a window
-----------------

To show a window, create a ``Tk()`` object and call its ``mainloop`` method.

``mainloop`` is blocking, so almost always will be the last line in your code.

.. code-block:: python

    win = tk.Tk()
    # Create widgets here
    win.mainloop()


Creating a widget inside a window
---------------------------------

To add a widget into a window, simply instantiate the widget and pass the window as the first argument. Note that the ``Widget`` class in this example is not real. See following examples for actual widgets.

.. code-block:: python

    w = Widget(window, keyword="argument")

This first argument is often referred to as the "master" or "parent".

Displaying a widget
-------------------

To display a widget inside its master, you need to assign it some space using a geometry manager.

There are three geometry managers to choose from: ``pack``, ``grid`` and ``place``. ``Place`` is rarely used, ``pack`` vs ``grid`` is personal preference.
Pack

Common arguments to pack include ``side``, ``fill`` and ``expand``.

.. code-block:: python

    w.pack(side=tk.LEFT, fill=tk.X, expand=1)

``side`` determines which side of the remaining space you wish to allocate your widget to, options include ``tk.LEFT``, ``tk.RIGHT``, ``tk.TOP`` and ``tk.BOTTOM``.

``fill`` determines which way to "stretch" out your widget. ``tk.X`` will stretch it horizontally and ``tk.Y`` vertically.

``expand`` tells the geometry manager whether you want the widget to change size with the window. Options are ``True`` or ``False`` (or 1 and 0).

Grid
----

Common arguments to grid include ``row``, ``column``, ``rowspan``, ``columnspan`` and ``sticky``.

.. code-block:: python

    w.grid(row=1, column=2, rowspan=2, columnspan=3, sticky="nsew")

``row`` and ``column`` define which cell to place the widget into``.

``rowspan`` and ``columnspan`` determine how many rows or columns the widget should occupy.

``sticky`` allows you to choose which sides of the cell the widget should stick to. It defaults to none, so the widget stays in the center of its cell. Arguments are variations of n,s,e,w (north, south, east, west) eg "ew" will stick horizontally and "ns" vertically.

Place
-----

Common arguments to place are ``x``, ``y``, ``relx`` and ``rely``

.. code-block:: python

    w.place(x=5, y=12)
    w.place(relx=0.5, rely=0.5)

``x`` and ``y`` are the coordinates you wish to place the widget at.

``relx`` and ``rely`` are relative coordinates to the master. 0.5 for both would be the center, as in the example above.
Choosing a widget

The widget you need depends on what you are trying to add to your window. Here are all of the widgets:

I want to
---------

Show some text
..............

In order to show text, create a ``Label`` widget and pass in the text to display as the "text" argument.

.. code-block:: python

    label = tk.Label(master, text="hello")


Collect user input (text)
.........................

To allow the user to enter text, you can use either an ``Entry`` or a ``Text`` widget. An ``Entry`` will collect a single line of text, whereas a ``Text`` can collect multiple lines.

.. code-block:: python

    entry = tk.Entry(master)
    text = tk.Text(master)


Collect user input (number)
...........................

To collect a number from the user, use a ``Spinbox`` widget. This widget also has arrows to allow the user to increment or decrement the number by clicking.

.. code-block:: python

    spinbox = tk.Spinbox(master)


Add a button
............

To add a button, use a ``Button`` widget. The text on the button is set with the "text" argument, and the function to run when the button is pressed is passed via the "command" argument.

Do not put the parentheses after the function passed to "command" as this will call the function and bind the result instead of binding the function itself.

.. code-block:: python

    submit_button = tk.Button(master, text="submit", command=submit_answer)


Add a checkbox
..............

A checkbox can be created using the ``Checkbutton`` widget.

The text displayed by the ``Checkbutton`` can be passed with the "text" argument.

.. code-block:: python

    show_line_numbers = tk.Checkbutton(master, text="Show line numbers?")

Letting the user make a choice
------------------------------
Add radio buttons
.................

A radiobutton can be created using the ``Radiobutton`` widget. The chosen value is stored in a tkinter variable (see tkinter variables)

``Radiobuttons`` are very similar to ``Checkbuttons`` in terms of arguments.

.. code-block:: python

    choice = tk.IntVar(master)
    one_box = tk.Radiobutton(master, text="one box", value=1, variable=choice)
    two_boxes = tk.Radiobutton(master, text="two boxes", value=2, variable=choice)
    three_boxes = tk.Radiobutton(master, text="three boxes", value=3, variable=choice)

Add a list box
..............

A ``Listbox`` widget is sort of like an expanded dropdown. The user can select one or more choices depending on configuration

.. code-block:: python

    choices = ("small", "medium", "large")
    choice_display = tk.Listbox(master, selectmode=tk.SINGLE)
    choices.insert(tk.END, *choices)

Add a dropdown menu
...................

The ``OptionMenu`` widget functions like a dropdown box.

Unlike a ``Listbox`` you can only select one item at a time

The ``OptionMenu`` widget requires a tkinter variable (see tkinter variables)

.. code-block:: python

    choices = ("small", "medium", "large")
    var = tk.StringVar(master)
    choice_display = tk.OptionMenu(master, var, *choices)

Group some widgets
------------------

To group widgets, create a ``Frame`` widget and use it as the widgets' master. This allows for greater control with the ``pack`` geometry manager and allows you to set the background colour of certain sections of your application (with the ``bg`` argument).

.. code-block:: python

    left_frame = tk.Frame(master, bg="red")
    button = tk.Button(left_frame, text="hello", command=say_hello)

Using variables in widgets
--------------------------
Types of tkinter variables
..........................

There are four variables in tkinter - ``BooleanVar``, ``DoubleVar``, ``IntVar`` and ``StringVar``. Create one like so:

.. code-block:: python

    bv = tk.BooleanVar(master)
    dv = tk.DoubleVar(master)
    iv = tk.IntVar(master)
    sv = tk.StringVar(master)

Setting the value manually
..........................

set the value using the ``set`` method.

.. code-block:: python

    sv = tk.StringVar(master)
    sv.set("hello")

Getting the value
.................

get the value using the ``get`` method.

.. code-block:: python

    hello_text = sv.get()

Attaching variables to widgets
------------------------------
I want to use a variable with a
...............................
Label
.....

You can attach a ``StringVar`` to a Label using the ``textvar`` argument.

Attaching a ``StringVar`` to a ``Label`` allows you to dynamically update the text displayed by the ``Label`` each time you call ``set()``.

.. code-block:: python

    sv = tk.StringVar(master)
    sv.set("hello")
    label = tk.Label(master, textvar=sv)

Entry
.....

You can attach a ``StringVar`` to an ``Entry`` using the ``textvar`` argument.

The value of the ``StringVar`` will update when the user types into the ``Entry``.

.. code-block:: python

    sv = tk.StringVar(master)
    sv.set("hello")
    entry = tk.Entry(master, textvar=sv)

Checkbutton
...........

You can attach a variable to a ``Checkbutton`` using the ``variable`` argument.

The value of the variable defaults to 1 when checked and 0 when not. You can change this by passing the ``onvalue`` and ``offvalue`` arguments.

.. code-block:: python

    iv = tk.IntVar(master)
    chk = tk.Checkbox(master, text="display line numbers", variable=iv)

    ##########

    sv = tk.StringVar()
    sv.set("no")
    chk = tk.Checkbox(master, text="go north?", variable=sv, onvalue="yes", offvalue="no")

Spinbox
.......

You can attach a variable to a ``Spinbox`` using the ``textvar`` argument.

This is the way to set a default value to a ``Spinbox`` too.

.. code-block:: python

    iv = tk.IntVar(master)
    iv.set(3)
    spin = tk.Spinbox(master, text="display line numbers", textvar=iv)

Radiobutton and OptionMenu
..........................

See radio buttons and dropdown menu widget recipes.

Changing Window Attributes
--------------------------
Changing the window's titlebar text
...................................

To change the text in the titlebar of your application, use the ``title`` method.

.. code-block:: python

    window.title("My Text Editor")

Changing the window's size
..........................

To change the size, use the ``geometry`` method. Pass in a string formatted as "widthxheight". You don't need to use the ``format`` method here, but it could increase readability.

.. code-block:: python

    width = 200
    height = 100
    geometry_string = "{}x{}".format(width, height)
    window.geometry(geometry_string)

Changing the window's position
..............................
To change the position, use the ``geometry`` method once again. Pass in a string formatted as "widthxheight+x+y".

.. code-block:: python

    width = 200
    height = 100
    x_pos = 300
    y_pos = 400
    geometry_string = "{}x{}+{}+{}".format(width, height, x_pos, y_pos)
    window.geometry(geometry_string)

Prevent Resizing
................

You can allow or prevent resizing with ``resizable``.

.. code-block:: python

    window.resizable(False, False)  # Not resizable
    window.resizable(True, False)   # cannot resize in Y direction
    window.resizable(False, True)   # cannot resize in X direction

Minimum and maximum size
........................

You can force a minumum and maximum size with ``minsize`` and ``maxsize``.

.. code-block:: python

    window.minsize(width=200, height=400)
    window.maxsize(width=600, height=1200)

Adding a Top Menu
-----------------
Creating the Menu Bar
.....................

To add a menu bar to your application, first you need to create a ``Menu`` widget. You then assign this to your window's menu attribute. This will add a blank menu bar to the top of your window.

.. code-block:: python

    menubar = tk.Menu(window)
    window.config(menu=menubar)

Adding Menu Buttons
...................

In order to add single menu items, use ``add_command``. The text displayed on the item is passed in via the ``label`` argument, and the function to run by the ``command`` argument.

.. code-block:: python

    def say_hi():
        print("hi")

    menubar.add_command(label="greet me", command=say_hi)

Adding Sub-Menus
................

To add a sub-menu (like the "file" or "edit" menus you may be used to) create another instance of a ``Menu`` widget and pass it to the ``add_cascade`` method of your main menu bar. The ``label`` argument will be the text displayed, and the ``menu`` argument will be the sub-menu to add. By default, the sub-menu can be pulled off of the main menu bar. To prevent this, use ``tearoff=0``.

.. code-block:: python

    file_menu = tk.Menu(window, tearoff=0)
    file_menu.add_command(label="open", command=open)
    file_menu.add_command(label="save", command=save)

    menubar.add_cascade(label="File", menu=file_menu)

Adding a Checkbutton or Radiobutton in a Menu
.............................................

You can add ``Checkbutton`` and ``Radiobutton`` widgets into menus with ``add_checkbutton`` and ``add_radiobutton``.

.. code-block:: python

    show_line_nums = IntVar(window)
    file_menu.add_checkbutton(label="Show Line Numbers", variable=show_line_nums)

    #####

    text_colour = StringVar(window)
    text_colour.set("black")
    file_menu.add_radiobutton(label="black text", variable=text_colour, value="black")
    file_menu.add_radiobutton(label="white text", variable=text_colour, value="white")
    file_menu.add_radiobutton(label="grey text", variable=text_colour, value="grey")

Adding a Separator
..................

You can add a space between menu items with ``add_separator()``.

.. code-block:: python

    file_menu.add_separator()

Dynamic Content
---------------
Swapping the contents of a window
.................................

To easily swap the contents of a window, for example if emulating a paged interface, create each "page" inside a separate ``Frame``. Then, use ``frame.pack_forget()`` or ``frame.grid_forget()`` (depending on your choice of geometry manager) to un-display the first frame. Follow up by using ``pack`` or ``grid`` to display the next.

.. code-block:: python

    def show_page_two():
        page_one.pack_forget()
        page_two.pack()

    def show_page_one():
        page_two.pack_forget()
        page_one.pack()

    page_one = tk.Frame(master)
    button_one = tk.Button(page_one, text="page two", command=show_page_two)
    label_one = tk.Label(page_one, text="on page one")
    label_one.pack(fill=tk.X)
    button_one.pack()
    page_one.pack()

    page_two = tk.Frame(master)
    button_two = tk.Button(page_two, text="page one", command=show_page_one)
    label_two = tk.Label(page_two, text="on page two")
    label_two.pack(fill=tk.X)
    button_two.pack
    # don't pack page_two

Creating a second window
........................

If you need another window, use a ``Toplevel`` widget. This can be built up in the same way as a normal ``Tk`` or ``Frame`` widget. A ``Toplevel`` displays upon creation, so typically I will subclass it (e.g. ``class MySubclass(tk.Toplevel):``), create its child widgets within ``__init__``, then display it by calling ``MySubclass(master)``.

.. code-block:: python

    popup = tk.Toplevel(master)
    label = tk.Label(popup, text="look at this popup window!")
    label.pack()
