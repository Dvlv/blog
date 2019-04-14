:Title: Some common python interview questions
:Date: 2018-12-23
:Tags: Python
:Category: Python

My first job out of university was entirely LAMP, using Zend framework. However, I had always loved writing python, and when it came time to leave that job I was mainly looking for something which would let me use it.

As you can imagine, recruiters were slightly sceptical of my lack of industry experience and would often ask me to prove my knowledge with a sort of "phone pop-quiz". I think I went through about six of these in total. 

This post will go through all questions which I can remember in a rough order of how often I had to answer them, with the best answer I can come up with (or link to).
If you are preparing for an entry-level job interview this should hopefully come in handy.

Anything in brackets will be my personal thoughts, not part of the answer.


What is the difference between a tuple and a list?
--------------------------------------------------

A tuple is immutable, which means it cannot be edited after creation. A list is mutable, which means it CAN be edited.

Potential bonus points: A tuple is usually used to store grouped but dissimilar data, whereas a list typically stores similar data. E.g. when processing people's measurements, you may store all of a single person's measurements in a tuple, but when comparing multiple people's shoe sizes you would store them in a list.

*(I was asked this by EVERY interviewer).*

*(I was also asked "What error do you get if you try to mutate a tuple" once. Answer is* ``TypeError`` *for python 2.7 and 3.7)*


What is the difference between a function and a method?
-------------------------------------------------------

A method belongs to a Class. A function does not.


What is a static method?
------------------------

A function which belongs to a class but does not require access to its attributes.

*(I was asked this almost every time, and it was always followed by: )*


Why would you use a static method over a function?
--------------------------------------------------

To keep it near where it would be used, which makes large projects easier to understand.

*(Most people who asked about static methods also asked about class methods next.)*


What is a class method and how is it different from a static method?
--------------------------------------------------------------------

A class method acts on a class, whereas a regular method acts upon an instance of a class. Class methods take "cls" as their first argument (the class itself), regular methods take "self" (the instance) and static methods have no standard first argument.


What is a generator?
--------------------

A function which uses the ``yield`` keyword to return values as required, so that the entire list of results is not in memory.


What are the advantages and disadvantages of a generator?
---------------------------------------------------------

Advantage: All results are not stored in memory, so a smaller amount needed.

Disadvantage: Results are consumed once returned, so you need to explicitly save any results you need more than once. 


What is a decorator?
--------------------

A function which "wraps" another function, allowing it to execute code before and after the wrapped function runs.


*(Once I was then asked "So why would you use a decorator?". I basically repeated the previous answer.)*


What is a metaclass?
--------------------

A class which is used to create other classes. The best answer(s) can be found here: `What are metaclasses in python? <https://stackoverflow.com/questions/100003/what-are-metaclasses-in-python>`_


What are the three main keywords for Error handling?
----------------------------------------------------

``Try`` - The code which may throw an error.

``Except`` - What to do if there is an error.

``Finally`` - Only executed if there is not an error.


How can you run code in parallel?
---------------------------------

*(Something like that, but I think it was worded better)*


You can use the ``Threading`` module to run code in multiple threads, or the ``multiprocessing`` module to use multiple processes.


What is thread safety?
----------------------

*(Not really python specific but I was asked it at least twice.)*

If something is thread safe then no two threads will operate on the same bit of data unexpectedly, which can cause unforseen errors.


What is a list comprehension?
-----------------------------

*(I was asked this once, I know what it is but its not something you can easily explain on the phone... )*

You create a list on a single line of code using a for loop and sometimes an if statement.


How do you do a switch statement in python?
-------------------------------------------

*(Sneaky sneaky)*

You can't, just use ``if`` and ``elif``.



