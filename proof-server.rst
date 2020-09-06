:Title: Writing a Social Proof Service in Python and SocketIO
:Date: 2020-08-01
:Tags: Python
:Category: Python

For the unaware, a social proof service is typically an externally-hosted SaaS product which, when integrated with a customer's website, displays a
small notification card at the bottom of the screen whenever a person performs some action on that website, such as signing up for a newsletter or buying
a product.

Providers of these services claim that using them will increase converstion rates. Obviously this must be taken with a grain of salt, as these
people are trying to sell their service! Still, it is an interesting thing to consider.

I was interested in the technology behind this, and thought that the concept would work rather well for one of the websites I run at work. This lead
me to attempt to make such a service myself. As it turns out, it is incredibly simple to do.

An outline of how it works
==========================

I have a site which acts as a sort of CMS (it's a really difficult one to explain). The front-end of this site is using a drag-and-drop website builder, and
it communicates with the CMS via liberal use of javascript's ``innerHTML``. 

The initial goal was to integrate the social-proofing service to the rest of the CMS, since the front-end already talks to it.

It didn't seem to be possible to do this, but luckily ``flask_socketio`` made the actual server-side so simple that this became no problem at all.

In its final form, the server-side involved a very small file, similar in size to a hello_world flask tutorial, and an HTML snippet which would
just need to be pasted into the homepage of the website-builder to handle everything else.

Writing the server
==================

The server makes use of the aforementioned ``flask_socketio`` to run a very simple server. An ``app`` is created just like a normal flask site, and
a single route is added, which is responsible for performing the broadcast to all connected sockets.


.. code-block:: python

    from flask import Flask, request
    from flask_socketio import SocketIO

    app = Flask(__name__)
    app.config.from_object(__name__)
    app.secret_key = config["flask"]["secret_key"]
    socketio = SocketIO(
        app,
        cors_allowed_origins=[
            "http://127.0.0.1:5000",
            "https://my-domain.com",
        ],
    )

    API_KEY = config["proof"]["api_key"]


    @app.route("/signup", methods=["POST"])
    def on_signup():
        """
        When POSTed to, sends a socketio message telling
        all listening visitors that an offer was just
        taken out.
        """
        data = request.form

        api_key = data.get("api_key")
        if not api_key or not api_key == API_KEY:
            return "Bad or missing API key", 401

        img_path = data["img_path"] or "default"
        offer_name = data["offer_name"] or "one of our partners"
        user_name = data["user_name"] or "someone"

        socketio.emit(
            "signup",
            {"img_path": img_path, "offer_name": offer_name, "user_name": user_name},
        )

        return "thanks"


    if __name__ == "__main__":
        socketio.run(app, port=6009)


We set up a ``flask_socketio`` server in much the same way as we would a regular flask server. The urls of our website
are passed to the ``cors_allowed_origins`` so that random people cannot send messages to our socket server. The ``api_key``
acts in much the same way.

The POST request takes 3 parameters, and we will see what these are used for in the client code now.

Writing the client
==================

The client is a bit more complicated than the server, as it has to handle injecting the HTML, the logic for displaying it, and a rather crude queue to
handle the case when multiple sign-ups happen in quick succession (which we know is absolutely the case with this particular site).

.. code-block:: javascript

    function onSignup(data) {
        if (!window.popupIsVisible) {
            showPopup(data);
        } else {
            window.backlog.push(data);
        }
    }


    function displayFromBacklog() {
        if (window.backlog.length && !window.popupIsVisible) {
            showPopup(window.backlog.shift());
        }
    }

    function afterCooldown() {
        if (!window.popupIsVisible && window.backlog.length) {
            displayFromBacklog();
        }
    }

    function hidePopup() {
        document.getElementById('proof-popup').classList.remove('visible');
        window.popupIsVisible = false;

        setTimeout(afterCooldown, 2000);
    }

    function showPopup(data) {
        var offerName = data["offer_name"];
        var userName = data["user_name"];
        var imgPath = data["img_path"];

        var imageTarget = document.querySelector('#proof-popup #image img');
        var messageTarget = document.querySelector('#proof-popup #message');

        imageTarget.src = imgPath;
        messageTarget.innerText = userName + " just signed up with " + offerName + "!";

        document.getElementById('proof-popup').classList.add('visible');
        window.popupIsVisible = true;

        setTimeout(hidePopup, 3500);
    }

    function addPopupHtml() {
        var popupMain = document.createElement('div');
        popupMain.id = 'proof-popup';

        var popupImgDiv = document.createElement('div');
        popupImgDiv.id = 'image'

        var popupImg = document.createElement('img');

        var popupMessage = document.createElement('div');
        popupMessage.id = 'message'

        popupMain.appendChild(popupImgDiv);
        popupImgDiv.appendChild(popupImg);

        popupMain.appendChild(popupMessage);

        document.body.appendChild(popupMain);
    }

    window.popupIsVisible = false;
    window.backlog = [];

    addPopupHtml();

    var socket = io("http://127.0.0.1:6009");

    socket.on('connect', function() {
        console.log("im connected");
    });

    socket.on('signup', onSignup);


Quite a lot to take in, so we'll go over it piece by piece.

The function ``addPopupHtml`` is used to add a few HTML elements to the end of the <body> of the page, which will contain the
popup itself. We have a wrapping div, a thumbnail image, and some text. The image will be populated by what is posted to
the ``img_path`` parameter. The text will be built from the ``offer_name`` and ``user_name`` parameters.

After injecting this HTML, we connect to our socket server and add a listener to the "signup" event. This is the event
emitted from our server's route, which sends the three POST parameters to our javascrupt. 

If a popup is already visible, we will add the received data to our backlog, otherwise we have a popup to show.

The ``showPopup`` function takes those three pieces of informtion and does the filling-in of the injected popup
HTML, then assigns the "visible" class to the popup's wrapper div, which makes it display to the user. 

We also set a global ``popupIsVisible`` variable against the window, and kick off a function to hide the popup again
after 3 and a half seconds.

Hiding the popup is as simple as just removing the "visible" class from it, and then we set another timeout to check
for the presence of a backlog.

If we have a backlog, we ``shift`` the oldest set of information from the backlog and display it as a popup in the
same way.

Now that the logic is all in place, let's see how the styling is making that "visible" class work.

The styling
===========

.. code-block:: css

    #proof-popup {
        position: fixed;
        display: flex;
        flex-direction: row;
        justify-content: space-evenly;
        align-items: center;
        bottom: 10px;
        right: -320px;
        -webkit-transition: right 1.25s ease;
        transition: right 1.25s ease;
        width: 310px;
        height: 100px;
        border-radius: 20px;
        background: #452462;
        color: white;
        border: 2px solid #ddd;
        font-family: "Roboto", 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
    }

    #proof-popup.visible {
        right: 10px;
    }

    #proof-popup #image {
        width: 20%;
        height: auto;
        float: left;
    }

    #proof-popup #image img {
        width: 100%;
    }

    #proof-popup #message {
        width: 70%;
        float: left;
    }

    @media screen and (max-width: 767px) {
        #proof-popup {
            width: 90%;
            bottom: -105%;
            right: 5%;
            -webkit-transition: bottom 1.25s ease;
            transition: bottom 1.25s ease;
        }

        #proof-popup.visible {
            bottom: 2.5%;
            right: 5%;
        }

    }


Not a great deal to say about this, it's making the popup appear as a small, rounded box in the corner of a desktop screen, or the center-bottom of a
mobile screen.

On desktop, the popup will animate in from the right of the screen, then animate away by going left again. On a mobile, it
comes in from the bottom, then hides back down again.

Putting it together
===================

So now we have a server listening for a POST request, and a client which establishes a socket connection to this server. All that's left to do now
is integrate it with our website.

This is as simple as sending a POST request whenever a user signs up. I won't show the real production code here, since it's wrapped in business logic,
but hopefully you know how to send a POST request contianing the three parameters from the earlier server code.

With this all in place, I just had to provide a small snippet of HTML to the person who builds the front end.

.. code-block:: html

    <script src="https://cdnjs.cloudflare.com/ajax/libs/socket.io/2.3.0/socket.io.js" integrity="sha256-bQmrZe4yPnQrLTY+1gYylfNMBuGfnT/HKsCGX+9Xuqo=" crossorigin="anonymous"></script>
    <link rel="stylesheet" href="https://my-domain.com/static/css/proof.css">
    <script src="https://my-domain.com/static/js/proof.js"></script>
