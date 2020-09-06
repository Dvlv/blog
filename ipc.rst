:Title: How to do Inter-Process Communication (IPC) w/ Python and Rust
:Date: 2019-12-14
:Category: Python
:Tags: Python, Rust

The way in which two programs written in two different languages communicate with eachother (fancy name IPC) is something which has interested me 
for a while. I was under the impression that each language would have to specifically accommodate each other language individually. 

Luckily this is not actually the case, and in this post I will outline three different ways of performing IPC which I have discovered recently.

The examples I will provide will allow for Python code to call Rust code, but each concept is likely supported over any vaguely-popular programming 
language.

1. Unix Sockets
===============

(Anyone unlucky enough to be following along on Windows will not be able to copy this example without using WSL or a virtual machine)

Setting up a unix socket involves one program being a server and listening on the socket, and another being a client and writing to it. 

For the example, I will write a program in Rust which gets the temperature of my CPU every 3 seconds and sends it over a unix socket, where it can be listened to by 
python, which can then display the temperature.


.. code-block:: rust

    extern crate systemstat;
    use std::sync::mpsc::channel;
    use std::thread;
    use std::time::Duration;

    use std::os::unix::net::UnixStream;
    use std::io::prelude::*;

    use systemstat::{System, Platform};

    fn main() {
        let sys = System::new();
        let mut stream = UnixStream::connect("/home/dvlv/Programming/rust/ipc/ipc.sock").unwrap();

        let (tx, rx) = channel();

        thread::spawn(move || {
            loop {
                thread::sleep(Duration::from_secs(3));

                match sys.cpu_temp() {
                    Ok(cpu_temp) => {
                        tx.send(format!("Temp is: {}", cpu_temp));
                    },
                    Err(x) => {
                        tx.send("error".to_string());
                    }
                };
            }

        });

        loop {
            let _ = rx
                .try_recv()
                .map(|reply| stream.write_all(reply.as_bytes()));
        }
    }
     
To follow along, add ``systemstat="0.1.5"`` to ``Cargo.toml``.

This program spawns a socket called ``ipc.sock`` and does a loop to write the results of ``sys.cpu_temp()`` to it every three seconds.

Now we need some python code to listen on this socket.


.. code-block:: python
    
    import socket
    import os
    import sys

    socket_path = "/home/dvlv/Programming/rust/ipc/ipc.sock"

    try:
        os.unlink(socket_path)
    except OSError:
        pass

    s = socket.socket(socket.AF_UNIX, socket.SOCK_STREAM)
    s.bind(socket_path)

    s.listen()

    while 1:
        conn, addr = s.accept()
        try:
            while 1:
                data = conn.recv(16)
                if data:
                    print("received ", data)
        finally:
            conn.close()

All this code does is listen in on our socket with an infinite loop and print what is sent over it. I was considering making a full tkinter GUI and displaying 
the temperature info from rust inside it, but I think this gets the point across for now. I suppose I'll leave the tkinter'ing as an exercise for the reader.

Unix sockets are incredibly simple to implement, but this example does not allow the Python program to send data to the Rust program, so let's look at a way of doing that 
next.

2. RPC
======

Remote Procedure Calling allows a program to expose some methods over a server for other programs to call. This is typically implemented via XML. Python has 
XMLRPC built in, and Rust has a couple of crates for it.

We'll set up a Rust server for the python application to call a simple method, since this is what we may actually want to do in the real world, since python 
is notoriously slow.

.. code-block:: rust

    extern crate xml_rpc;

    use xml_rpc::{Fault, Server};
    use std::net::{IpAddr, Ipv4Addr, SocketAddr};
    use serde::{Serialize, Deserialize};

    #[derive(Clone, Debug, Serialize, Deserialize)]
    struct MoveRightParams {
        pub point: Point,
        pub m: i64,
    }


    #[derive(Clone, Debug, Serialize, Deserialize)]
    struct Point {
        pub x: i64,
        pub y: i64,
    }

    fn move_right(mut p: MoveRightParams) -> Result<Point, Fault> {
        p.point.x += p.m;

        Ok(p.point)
    }

    fn main() {
        let socket = SocketAddr::new(IpAddr::V4(Ipv4Addr::new(127, 0, 0, 1)), 8080);
        let mut server = Server::new();

        server.register_simple("move_right", &move_right);

        let bound_server = server.bind(&socket).unwrap();

        println!("Running server");
        bound_server.run();
    }
 

To follow along, add ``xml-rpc="0.0.12"`` and ``serde = { version = "1.0", features = ["derive"] }`` to ``Cargo.toml``.

This code contains a struct representing a point with x and y coordinates, and another struct representing the parameters which will be needed to call 
the exposed function ``move_right``.

When calling this function, the client must provide two parameters, a ``Point`` with an ``x`` and ``y``, and the amount to move ``m``.

With the server running, we can now implement some python code which will send point coordinates and a movement amount and receive a reply from Rust's ``move_right`` function.


.. code-block:: python

    import xmlrpc.client

    proxy = xmlrpc.client.ServerProxy("http://127.0.0.1:8080")

    point_x = input("X coord ")
    point_y = input("Y coord ")

    params = {"point": {"x": point_x, "y": point_y}, "m": 2}

    print(f"moving({point_x},{point_y}) right by {params['m']}")

    move_right = proxy.move_right(params)

    print(move_right)


Running the following code will ask you for an x and y coordinate to move, then put them into a simple dictionary with keys matching the arguments in ``MoveRightParams`` 
from the Rust code.

We can then simply use ``proxy.move_right(params)`` to ask the Rust RPC server to perform its ``move_right`` and return the results back to Python in the ``move_right`` 
variable.

.. code-block:: bash

    dvlv@Zeus ~/P/r/ipc> python3 pycode/r.py 
    X coord 3
    Y coord 5
    moving(3,5) right by 2
    {'x': '5', 'y': '5'}


And there we go, we have just called a method written in Rust from Python code, and it was pretty seamless!

As a bonus, we'll go over one more example, and one which we actually use at work - using a queue/broker system.

3. ZeroMQ
=========

(We actually use RabbitMQ at work, but ZMQ is super easy to set up).

To follow along, you will need to install ZeroMQ on your machine. If using Arch linux, you can install ``zeromq`` and ``python-pyzmq`` with pacman. Then there's no 
further setup required.

We'll use Rust to set up the zmq server and listen for requests to execute once again.

.. code-block:: rust

    use std::thread;
    use std::time::Duration;

    extern crate zmq;

    fn reverse_word(word: &str) -> String {
        let rev: String = word.chars().rev().collect();

        rev
    }

    fn main() {
        let context = zmq::Context::new();
        let responder = context.socket(zmq::REP).unwrap();

        assert!(responder.bind("tcp://*:5555").is_ok());

        let mut msg = zmq::Message::new();

        loop {
            responder.recv(&mut msg, 0).unwrap();
            println!("received: {}", msg.as_str().unwrap());

            thread::sleep(Duration::from_millis(1000));

            let response: String = reverse_word(msg.as_str().unwrap());
            responder.send(response.as_bytes(), 0).unwrap();
        }
    }


I won't go over how zmq itself works, because I would be lying if I implied I understood it yet, but this example should be fairly easy to follow.

Rust sets up a zmq server and listens for incoming messages. When one arrives, it passes it to the ``reverse_word`` function and sends back the result.

Now we can use python to connect to this queue and send over some text to be reversed.


.. code-block:: python

    import zmq

    context = zmq.Context()

    socket = context.socket(zmq.REQ)
    socket.connect("tcp://localhost:5555")

    string_to_rev = input("enter string ")
    print(f"sending {string_to_rev}")

    socket.send(string_to_rev.encode())
    message = socket.recv()
    print(f"reversed: {message}")


We open a socket connection to port 5555 and send over the string entered by the user. We then listen for Rust's reply and print it to the console.


.. code-block:: bash

    (env) dvlv@Zeus ~/P/r/i/pycode> python3 z.py
    enter string reverse me please
    sending reverse me please
    reversed: b'esaelp em esrever'


With this, we now know three ways which we can call code written in one language from a program written in another.

Code examples are all available `on github here <https://github.com/Dvlv/ipc>`_

