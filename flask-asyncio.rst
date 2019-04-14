:Title: Using asyncio to speed up Flask API calls
:Date: 2019-02-10
:Tags: Python
:Category: Python

For a work project I am currently building, I have been tasked with creating a stats page for a particular site. This is completely the norm at my workplace, with one exception.

Typically we have an in-house database / service for amalgamating all sorts of numbers and dates, and we query this service and put its output into DataTables. Stats done!

For my project, the stats are in two separate places - Google Analytics, and another company that we partner with.

To top it off, this other company do not have a very flexible API. When generating "site overview" stats about each product on that site, 
we require one api call *per product* to get all of this information. As you can imagine, this makes the stats rather slow to load. 

I wanted to find a way to speed this particular page up without making a drastic change to the structure of the whole site. I was familiar with asyncio, but didn't realise it could be integrated into a Flask application pretty painlessly. 

In this post I will show how I have used ``asyncio`` and ``aiohttp`` to speed up the aforementioned stats page. Obviously I will have to "censor" some of the code as it deals with business logic, but hopefully the method will be easy to understand.


Adding an event loop to Flask
-----------------------------

This is stupid simple. Just open the file in which you declare your flask app (as in, ``app = Flask(__name__)``) and throw these lines in:

.. code-block:: python

    import asyncio

    event_loop = asyncio.get_event_loop()


Using the event loop to perform API calls
-----------------------------------------

Next, in the class which I am using to communicate with the external API, I defined the following method:

.. code-block:: python

    async def fetch_and_populate_dict(self, url: str, dictionary: dict, key: str):
        """
        Asynchronously calls a url with aiohttp and puts the resulting html into a
        dictionary against provided key.

        :param url: url to load
        :param dictionary: dict of return values
        :param key: key to add this result to into dictionary
        :return: ``None``
        """
        async with aiohttp.ClientSession() as session, async_timeout.timeout(180):
            full_url = self.base_url + url + "&api_key=" + self.api_key
            async with session.get(full_url) as response:
                dictionary[key] = await response.text()

This method takes a url path, a dictionary, and the required key. After building the full url, it then uses aiohttp to asynchronously fetch the url's contents and put them into the provided dictionary, under the given key.

Now to make use of this method:

.. code-block:: python

        def get_summary(self, other, args):

        # *wrangle some data*

        summary_urls = {c_id: get_summary_url(o_id, args, args) for c_id, o_id in some_dict.items()}

        all_summaries = {}
        all_data = {}

        summary_future = asyncio.gather(*(self.fetch_and_populate_dict(url, all_summaries, id) for id, url in summary_urls.items()))

        from web.project_name import event_loop
        event_loop.run_until_complete(summary_future)

        # Now process the all_summaries dictionary

Here I build a dictionary of ids to urls for some system-specific IDs (``get_summary_url`` just builds an API endpoint url path). 

With this information at hand, I then loop through the items of this dict, passing them as variables to ``fetch_and_populate_dict``. The resulting tuple is then itself expanded and passed as arguments to ``asyncio.gather``. 

With the futures all gathered, all that is left to do is import the ``event_loop`` from the project root and call its ``run_until_complete`` method on our gathered futures. 

This will asynchronously collect the results of a GET request to each generated URL and populate its result as a value of ``all_summaries``. 

An example
----------

To perhaps better explain this, imagine an API where a name is passed as a GET parameter and it simply returns ``"Hello {name}"``. Also imagine this takes a long time for the service to compute.

Given a list of names, we would first generate a dict of name => url mappings, such as:

.. code-block:: python

    names_to_urls = {
        "bob": "http://localhost:5000/greet/?name=bob",
        "bill": "http://localhost:5000/greet/?name=bill",
        "betty": "http://localhost:5000/greet/?name=betty"
    }

Then we define an empty dict which will be used to gather our data:

.. code-block:: python

    greets = {}

finally, we loop through the items in our ``names_to_urls`` and pass them to ``fetch_and_populate_dict``.

.. code-block:: python

    greet_future = asyncio.gather(*(self.fetch_and_populate_dict(url, greets, name) for name, url in names_to_urls.items()))

    event_loop.run_until_complete(greet_future)

    
Once the urls have been fetched, ``greets`` will look like so:

.. code-block:: python

    {
        "bob": "Hello bob",
        "bill": "Hello bill",
        "betty": "Hello betty"
    }

With the code in place, there is one thing to note: you cannot run this application in multiple threads. 

In practical terms, this means when developing you will need ``threaded=False`` and ``use_reloader=False`` in your call to ``app.run``. 

In production I have ``threads=1`` in my uwsgi.ini file (the same would be required if using gunicorn, too).

I hope this example helps you to understand how to use ``asyncio`` to speed up multiple API calls in a flask application. 
