:Title: Building a Desktop Clock widget with Tkinter
:Date: 2017-04-25
:Tags: Python, Tkinter
:Category: Tkinter

.. image:: {filename}/images/blog/clockwidget.png
   :width: 100%
   :alt: Clock Widget

A few months ago I bought a shiny new gaming laptop as my main computer. No surprise, this laptop came with Windows 10 installed. Since I decided to pass on Windows 8 and 8.1, this was my first experience with something other than Windows 7 for at least 8 years. Of all the things I thought I may miss, I didn't realise that the desktop widgets would be one of them.

I've always had the clock widget and the CPU/RAM dial on my second monitor, so I could keep an eye on the important things whilst I had a full screen game on my primary monitor. Windows 10 doesn't have a clock widget, and I don't have on in the room visible when I'm at my desk, so it's been a pain to keep an eye on the time whilst gaming.

For this reason, I decided to use my programming skillz to write a little clock widget for a secondary monitor. I chose to use tkinter for this, since it's my favourite GUI library for simple things like this.

In this post I'll talk through the code so that you can also make something like this if you find you need something like this.

.. code-block:: python

    import tkinter as tk
    import tkinter.ttk as ttk
    import threading
    import datetime

- ``tkinter`` and ``ttk`` will provide our GUI components. 
- ``threading`` allows our clock to update outside of the GUI's thread. 
- ``datetime`` will get us the time.

The first class we will create will be the thread for handling the time.

.. code-block:: python

    class TimeThread(threading.Thread):
        def __init__(self, master):
            super().__init__()
            self.master = master
            self.force_quit = False

        def run(self):
            while True:
                if not self.force_quit:
                    self.main_loop()
                elif self.force_quit:
                    del self.master.worker
                    return
                else:
                    continue
            return

        def main_loop(self):
            now = datetime.datetime.now().time()
            now_formatted = str(now).split('.')[0]
            self.master.update_time_remaining(now_formatted)

This thread is borrowed from the pomodoro timer in Chapter 7 of Tkinter By Example. It's a simple class which will call a ``main_loop`` function as long as it hasn't been told to ``force_quit``.

The ``main_loop`` function gets the current time, removes the trailing milliseconds from it and passes it to a function in the GUI thread called ``update_time_remaining``. Pretty simple stuff. Onto the GUI class.

.. code-block:: python

    class Timer(tk.Tk):
        def __init__(self):
            super().__init__()

            self.title("The Time")
            self.geometry("500x100")
            self.resizable(False, False)

            style = ttk.Style()
            style.configure("TLabel", foreground="black", background="lightgrey", font=(None, 50), anchor="center")

            self.main_frame = tk.Frame(self, width=500, height=300, bg="lightgrey")

            self.time_var = tk.StringVar(self)
            self.time_var.set("time")
            self.time_display = ttk.Label(self.main_frame, textvar=self.time_var)

            self.main_frame.pack(fill=tk.BOTH, expand=1)

            self.time_display.pack(fill=tk.X, pady=15)

            self.oron = False
            self.protocol("WM_DELETE_WINDOW", self.safe_destroy)
            self.wm_attributes('-topmost', 1)
            self.bind('<Control-f>', self.toggle_or)
            self.start()

Our ``__init__`` sets us up a 500x100 window containing a ``Label`` which will display the time, bound to a ``StringVar``.

We bind the window manager's closing to a function ``safe_destroy`` so that we can kill our ``TimeThread`` safely when the user closes the window. We also force the window to stay on the top with the ``-topmost`` attribute, and bind a function to ctrl-f.

Now that we have the layout, we need to handle starting the clock.

.. code-block:: python

    def setup_worker(self):
            worker = TimeThread(self)
            self.worker = worker

        def start(self):
            if not hasattr(self, "worker"):
                self.setup_worker()
            self.worker.start()

        def update_time_remaining(self, time_string):
            self.time_var.set(time_string)
            self.update_idletasks()

Our ``start`` method will create and bind a ``TimeThread`` instance to ``self.worker`` if one does not exist, then start the thread.

``update_time_remaining`` sets the value of our ``StringVar`` to the time string returned by the ``TimeThread``. It then updates the GUI with ``update_idletasks``.

We can use ctrl+f to get rid of the window border for aesthetic purposes. This is done like so:

.. code-block:: python

    def toggle_or(self, event=None):
        if not self.oron:
            self.overrideredirect(1)
            self.oron = True
        else:
            self.overrideredirect(0)
            self.oron = False

This function toggles ``overrideredirect`` (abbreviated to 'or') when we press ctrl+f.

Finally, let's look at how we can safely end the ``TimeThread`` when we close the application.

.. code-block:: python

    def safe_destroy(self):
        if hasattr(self, "worker"):
            self.worker.force_quit = True
            self.after(100, self.safe_destroy)
        else:
            self.destroy()

We set the ``TimeThread``'s ``force_quit`` to True, causing it to break out of its loop, remove itself from our GUI thread and return. Then, after 0.1 seconds we call this method again. If the ``TimeThread`` has terminated, we won't have a ``worker`` attribute anymore, so we can destroy this window. This ensures the ``TimeThread`` has definitely exited before we end the program.

All that's left to do is run this.

.. code-block:: python

    if __name__ == '__main__':
        root = Timer()
        root.mainloop()

That's it for our desktop clock widget. If you want to you can customise the font and colours to your liking using the ttk styling. As always, the full code is on my github.

