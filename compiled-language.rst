:Title: Learning a Compiled Language as a Python Fan
:Date: 2019-04-14
:Category: Python
:Tags: Python, Programming


It's probably no secret that python is my favourite programming language. The speed at which you can get a complicated program up and running is amazing. 
My day job as a web developer is one of the places where python shines the most, and so I get to work with it on a daily basis.
Not only that, but there is also a game engine which is both Free software, and has its own language which is based around python - Godot.

While gdscript isn't *exactly* python, it's so close that learning it was almost no effort. The only thing that ever gets me is remembering to put ``var`` in front
of variables!

Despite my happiness with the language, I am made aware of python's main drawback on a couple of projects at work. 

Python is slow. 

When it comes to big data-processing tasks python can cope, but it is far from the best tool for the job. 

My workplace likes to roll its own analytics tools, which means we have a relentless supply of data from multiple sources which need to be managed and stored in the 
right place, as well as easily queried. Whilst it is *possible* to use some kind of queue, such as ``celery``, to ease the pressure off of our system, this is ultimately 
a task which python is the wrong tool for. 

There is also the problem of cross-platform distribution of tools. When I wrote my second book, (`Tkinter GUI Programming By Example <https://www.dvlv.co.uk/pages/tkinter-gui-programming-by-example.html>`_) for Packt, I ended the book with a chapter on how to compile and release a python GUI application. I have never really felt the need to do this before, so the difficulty was eye-opening for me. Unless you're distributing as source and telling the user to install python, or just via pip, there's no easy way to package for all platforms from a single one (meaning I need a windows machine to build an .exe, a Mac to build an .app, etc). 

So, in the interest of not niche'ing myself too much down into a python-bot, I've decided I want to branch out and learn a compiled language. The problem is, it's way too easy to compare them to python, quickly decide I don't like them, then give up.

It's also become clear to me that I can watch video tutorials and read all sorts of books / guides and understand perfectly what's going on. Then, as soon as it comes to writing something with that language, I realise I can't do it as instinctively as I can with languages I use every day. I think that's a general thing when it comes to learning programming - you can't just *watch*, you have to *do*! 

I set myself the challenge of building the same thing in a few different compiled languages. This "thing" had to be something which gives me an idea of the general experience of working in this language, but not be so complicated that it takes weeks, since I know of my tendencies to give up.

The project I chose was to rewrite a git helper I've been using for many years, written in python of course. I wrote it back in 2013 when my workplace-at-the-time moved from svn to git. It contains a few challenges which would hopefully give me an idea of how complex certain things would be in a particular language.

I would normally link to the python version now, but I don't make the source code publically available because I doubt anyone else would want it, and some iterations have contained business-specific commands. I will, however, show snippets of my attempts in the languages I try out.

The git helper
==============

The original helpers is essentially a script called along with some command line arguments which performs the verbose git commands in the background using ``os.system``. Since I came up with the name easy-git (which I later found out was `already a thing <https://people.gnome.org/~newren/eg/>`_) I use the script for a basic git workflow like so:

    - ``eg stat`` (git status)
    - ``eg add <file> <file>`` (git add <file> <file>)
    - ``eg cm adding a new feature`` (git commit -m "adding a new feature")
    - ``eg origin`` (git push origin <branch>)

The basic subset of features I wanted to complete with each version of the helper were as follows:
    - The "engine" of the file, parsing the first command line argument to determine what function to run
    - ``stat`` -> run ``git status``
    - ``cm`` -> run ``git commit`` with extra args as the commit message
    - ``branch`` -> gets current branch name
    - ``origin`` -> push current branch to origin
    - ``usage`` -> print something to the console telling the user how to use it when the supplied arguments are wrong.
    
With just this in place, as I was keeping each demo on Github, the resulting binary would eventually be able to be used to update itself.

Now I needed some languages to evaluate.

The language candidates
=======================

C++
---

During the beginning of my interest in game development I bought a book about C++. I thought the language was alright. I had only really been doing Java at the time, so it was mostly what I was used to. I also did a module at university which was exclusively in C++, so I have a *slight* bit of background on it.

Despite the fact that it gets a lot of smack-talk online (then again, which language doesn't) I can't deny the **huge** community behind it, which is very important in all stages of using a programming language.

D
-

Can't remember how I stumbled upon D, but it advertised itself as a nice middle between python and C++, so I had no real choice but to give this one a look.

The syntax looked fairly standard and the documentation for the standard library seemed very thorough, so I added it to the list.

Nim
---

Nim came up when I was researching D. Some folk on reddit were saying Nim is D's main competitor. I gave it a look and was (as you can predict) quite impressed by its syntax. It's quite similar to python, being whitespace-significant and having the same import structure. It's also very interesting that nim code compiles down to a few other languages, namely C, C++, Obj-C and JS. 

Needless to say, Nim went immediately on the list.

Rust
----

I began looking at rust a while back during a quiet time at work. While it looked very complicated, I liked what it was trying to accomplish as a fast and safe language. From a community standpoint, being backed by Mozilla also can't hurt. 

Go
--

We have some Go code at work, and while I don't like it much, one of my colleagues is adamant that it's an amazing language, and my experiences were tainted by bad code in an older version. I hesitantly threw this onto the last spot on my list.

Honorable Mentions
------------------

Some languages which I checked out but have struck from the list:

Pascal
......

I like the syntax of Pascal, but it seems quite dead at this point. I also find the fact that there are enterprise-distributions of the language very confusing.

C
.

I briefly considered C, but after some searching many people claim that it isn't too much faster than C++, and is mainly used when you need to get very close to hardware. That's not really my area of interest, so I gave C a pass.

Dart
....

I did a lot of looking into dart. I quite liked the look of it, but I have no idea where dart is as a language. I don't think version 2 can even be ahead-of-time compiled anymore, and it seems that dart's fate is with flutter. Nevertheless, if I find myself writing a mobile app which isn't a game, I am definitely keen to give flutter a look.


Crystal
.......

Crystal looks cool in that it uses ruby syntax but is a compiled language. Thing is I don't much like ruby's syntax, despite how similar it is to python. It was a brief consideration, but I don't think I would opt to use it over any of those I've already chosen, so I gave it a miss. 


Coding Begins
=============

With my list at a reasonable 5, it came time to start writing some code. I began with C++

C++
---

I heard from my uni days that writing ``using namespace std`` was terrible practise and would lose us marks, so I instead ended up writing a truckload of ``std::``, as well as mistyping a load of ``std:`` and getting annoyed.

The first thing I ran into when building the C++ version was the fact that it cannot ``switch`` on a string. I wanted to avoid a huge chain of if-else, so I had to come up with another idea.

That idea was to have a map of string to function references which I passed the first ``argv`` to. 
Since there are two types of function - those which take the rest of the ``argv`` and those which take no arguments - I split the "engine" up into two maps. The map checked will be determined by the length of ``argv`` (called ``argc`` in c++).


.. code-block:: c++

    int main(int argc, char **argv) {
        auto funcMap = buildNoArgsFuncMap();
        auto argsFuncMap = buildArrayArgsFuncMap();

        if (argc == 1) {
            return usage();
        }

        if (argc == 2) {
            if (funcMap.count(argv[1]) > 0) {
                funcMap[argv[1]]();
            } else {
                return usage();
            }
        } else {
            if (argsFuncMap.count(argv[1]) > 0) {
                std::vector<std::string> wantedArgs(argv+2, argv + argc);
                argsFuncMap[argv[1]](wantedArgs);    
            } else {
                return usage();
            }
        }    
    }


I've seen ``typedef`` capabilities in languages in the past but had absolutely no idea why anyone would use them. After about 15 minutes with C++, I now know exactly why. I had to create a couple of type aliases to make the ``funcMap`` returning functions nice to read.


.. code-block:: c++

    typedef void (*NoArgsFunc)(void);
    typedef void (*ArrayArgsFunc)(std::vector<std::string> args);
    typedef std::map<std::string, NoArgsFunc> NoArgsMap;
    typedef std::map<std::string, ArrayArgsFunc> ArrayArgsMap;
    

The first two here are pointers to the two types of function which I will be using, and the last two are the maps which will hold them. 

Now I could use them to create functions which return the maps.


.. code-block:: c++

    NoArgsMap buildNoArgsFuncMap() {
        NoArgsMap funcMap;
        funcMap["origin"] = funcMap["oriign"] = &origin;
        funcMap["stat"] = &stat;
        funcMap["status"] = funcMap["stt"] = funcMap["satt"] = funcMap["stat"];
        funcMap["branch"] = &branch;

        return funcMap;
    }


    ArrayArgsMap buildArrayArgsFuncMap() {
        ArrayArgsMap arrayArgsFuncMap;
        arrayArgsFuncMap["cm"] = &cm;

        return arrayArgsFuncMap;
    }
    

Aliasing misspellings of commands which I regularly get wrong was just as easy as in a switch statement.

With this in place, I first tackled the function which would get the branch name from git. This got split into three functions for simplicity.


.. code-block:: c++

    std::string getOutput(const char* cmd) {
        std::array<char, 128> buffer;
        std::string result;
        std::unique_ptr<FILE, decltype(&pclose)> pipe(popen(cmd, "r"), pclose);
        if (!pipe) {
            throw std::runtime_error("popen() failed!");
        }
        while (fgets(buffer.data(), buffer.size(), pipe.get()) != nullptr) {
            result += buffer.data();
        }
        return result;
    }

    std::string getBranchName() {
        std::string statusCommand = "git status";
        auto statusOutput = getOutput(statusCommand.c_str());

        std::stringstream outputStream(statusOutput);
        std::string firstLine;

        std::getline(outputStream, firstLine);

        std::string branchName = firstLine.substr(firstLine.find_last_of(" ")+1);

        return branchName;
    }

    void branch() {
            std::cout << getBranchName();
    }
    
    
``getOutput`` was shamelessly stolen from `this stackoverflow question <https://stackoverflow.com/questions/478898/how-do-i-execute-a-command-and-get-output-of-command-within-c-using-posix>`_, since it's not something I would ever have been able to figure out by myself. 

``getBranchName`` grabs the output of ``git status`` and splits off the first line, then splits off the last word. This is the current branch name. I must say, converting a string to a stream in order to get the first line from it is not something I've encountered before.

With ``branch`` written, ``origin`` and ``stat`` are trivial thanks to ``std::system``.


.. code-block:: c++

    void origin() {
        std::string gitCommand = "git push origin " + getBranchName();
        std::system(gitCommand.c_str());
    }
    
    void stat() {
        std::system("git status");
    }
    

I know how to split a string to an iterable, but now I had to do the reverse to write the ``cm`` function:


.. code-block:: c++

    void cm(std::vector<std::string> args) { 
        const char* const delim = " ";
        std::ostringstream imploded;
        std::copy(args.begin(), args.end(), std::ostream_iterator<std::string>(imploded, delim));
        std::string commitMessage =  imploded.str();
        std::string commitCommand = "git commit -m '" + commitMessage + "'";
        std::system(commitCommand.c_str());
    }
        

Again, the hard part of this is stolen from `stackoverflow <https://stackoverflow.com/questions/5689003/how-to-implode-a-vector-of-strings-into-a-string-the-elegant-way>`_. Imploding the vector into a string and passing it to ``std::system`` was easy enough.

Last thing to do is ``usage``. Since this is the same thing every time, I may not show this in future languages.


.. code-block:: c++

    int usage() {
        std::cout << "Use it properly!";
        return 0;
    }

    
And with that the C++ version is complete. While there were a couple of things which I would not have been able to figure out without stackoverflow, I think that's a problem for any language. It also showcased the large-community aspect of C++ which I mentioned earlier, and has proven how valuable it is when learning a language. 
Overall, I definitely didn't hate the language.


D
-

Unlike C++, D supports ``switch`` ing on a string. The ``main`` function also does not have to return an integer. This made the main engine very simple:


.. code-block:: d

    void main(string[] argv) {
        if (argv.length < 2) {
            return usage();
        }
        auto command = argv[1];
        switch (command) {
            case "stat", "stt":
                stat();
                break;
            case "branch":
                writeln(getBranchName());
                break;
            case "origin":
                origin();
                break;
            case "cm":
                if (argv.length > 2) {
                    cm(argv[2 .. $]);
                } else {
                    goto default;
                }
                break;
            default:
                usage();
                break;
        }
    }

A nice feature I found was ``goto default``. This means that if I were to change the default behaviour in the future, I would not have to find multiple cases of ``usage();`` to replace.

When it came to getting the branch name I found a way of spawning a background process and waiting for its output. It's very readable, much moreso than c++'s way.


.. code-block:: d

    string getBranchName() {
        auto pipes = pipeProcess(["git", "status"], Redirect.stdout | Redirect.stderr);
        scope(exit) wait(pipes.pid);
        
        string output = strip(pipes.stdout.readln());
        string branchName = output.split(" ")[$-1];
        
        return branchName;
    }


An interesting feature of D is the ``$`` alias. This is used in indexing to be the length of something - which is why I use ``[$-1]`` to access the last element of the ``output`` here, and above in ``main`` I can use ``[2..$]`` to slice to the end of ``argv``. 

Now ``stat`` and ``origin`` are again very simple:


.. code-block:: d

    void stat(){
        auto pid = spawnProcess(["git", "status"]);
        scope(exit) wait(pid);
    }

    void origin() {
        string branch = getBranchName();
        auto command = ["git", "push", "origin", branch];
        
        auto pid = spawnProcess(command);
        scope(exit) wait(pid);
    }
    
For ``cm`` I found an easier way to cope with the need for speech marks surrounding the ``-m`` flag. Instead of using ``pipeProcess`` D has ``spawnShell`` which functions much like ``system``.


.. code-block:: d

    void cm(string[] messagePieces) {
        auto commitMessage = join(messagePieces, " ");
        string command = "git commit -m '%s'".format(commitMessage);
        auto pid = spawnShell(command);
        wait(pid);
    }

Imploding an array to a single string is also much easier than in C++, with the provided ``join`` method.

Overall, D was a much easier and significantly quicker experience than C++. What is lacks in community size, it makes up for with very detailed documentation. Nothing was more than a couple of searches away. 

Nim
---

The first thing which stood out to me when writing in Nim was the default vim's lack of inbuilt syntax highlighting. I'm used to full-blown semantic highlighting, so coding a colour-free terminal was a tad daunting.

Despite this, Nim was an absolute joy to write, and definitely the quickest. Much like when writing gdscript, I find myself writing python and forgetting that it needs a keyword to declare variables.


Unlike any other compiled language I tried, nim does not require a ``main`` function. For a proper application, I would probably still write one and call it, but in this instance I didn't bother.


.. code-block:: nim

    let arguments = commandLineParams()
    if arguments.len > 0:
        case arguments[0]:
            of "cm":
                if arguments.len > 1:
                    commit(arguments[1..<arguments.len])
                else:
                    usage()
            of "branch":
                branch()
            of "origin":
                origin()
            else:
                usage()
    else:
        usage()
        
Unusual thing to note here is that ``commandLineParams`` doesn't pull in the binary name, so we ``switch`` on ``[0]`` instead of the usual ``[1]``.

Getting the branch name was absurdly simple.

.. code-block:: nim

    proc getBranchName(): string =
        let statusOutput = execProcess("git status")
        let firstLine = statusOutput.split("\n")[0]
        let branchName = firstLine.split()[^1]

        return branchName

    proc branch(): void =
        let branchName = getBranchName()
        echo branchName
        
The ``execProcess`` procedure returns stdout as a string, so it can be split and indexed just like one would imagine in python. The branch name is then grabbed off of the end, with ``[^1]`` being the equivolent of ``[-1]`` in python.

I didn't write ``stat``, but ``origin`` was just as easy.


.. code-block:: nim

    proc origin(): void =
        let command = "git push origin " & getBranchName()
        discard execCmd command
        
If you do not want to use a return value in Nim, it must be manually ``discard`` ed. Also, string concatenation is done with ``&``. This is strange but not really a bother.

Finally, ``cm`` was as easy as you may be imagining:


.. code-block:: nim

    proc commit(args: seq[string]): void = 
        let command = "git commit -m '" & args.join(" ") & "'"
        discard execCmd command

        
A ``seq[string]`` is an non-length-specified array. The rest should be perfectly readable.

Overall, nim is fantastic to work in. Both docs and beginner-walkthrough were excellent.


Rust
----

I gave up on Rust. It was definitely the most frustrating experience I've had with coding in a long while, and it's very obvious I'm not going to continue with it when there are other options available. 


.. code-block:: rust

    fn main() {
        let args: Vec<String> = env::args().collect();
        if args.len() < 2 {
            return usage();
        } else {
            match args[1].as_ref() {
                "cm" => {
                    if args.len() > 2 {
                        cm(&args[2..args.len()]);
                    } else {
                        usage();
                    }
                },
                "branch" => {
                    get_branch_name();
                },
                "origin" => {
                    origin();
                },
                _ => usage(),
            }
        }
    }
    

A switch statement is called a ``match`` in rust. Since there are multiple different types of string, you have to use ``as_ref`` to match with one.

Getting the output of a system command took *ages*. Now that I have it written out it all makes perfect sense, but building up these functions and getting them to work was a long and laborious process.


.. code-block:: rust

    fn run_command(cmd_to_run: std::string::String, args_for_cmd: &[&str]) -> std::result::Result<std::process::Output, std::io::Error> {
        let output = Command::new(cmd_to_run)
                            .args(args_for_cmd)
                            .output();
        return output;
    }

    fn get_command_output(cmd_to_run: std::string::String, args_for_cmd: &[&str]) -> std::string::String {
        let output = run_command(cmd_to_run, args_for_cmd);
        
        match output {
            Ok(o) => {
                let output_results: std::string::String = String::from_utf8_lossy(&o.stdout).to_owned().to_string();
                return output_results;
            },
            Err(_) => "fail".to_string(),
        }
    } 

    fn get_branch_name() -> std::string::String {
        let a = vec!["status"];
        let status_output = get_command_output("git".to_string(), a.as_slice());
        let first_line = status_output.split("\n").collect::<Vec<&str>>()[0];
        let pieces = first_line.split(" ").collect::<Vec<&str>>();
        let branch_name = pieces[pieces.len()-1];
        
        return branch_name.to_string();
    }


Since a function which throws an exception can return nothing, it's not possible to return a string from code which runs a command. This mandated it be in its own function, which I suppose is a good thing (as long as you know the reason, which I did not for a while). 

I really don't understand the string-wrangling that goes on in rust, so I never actually know how a string is supposed to be until the compiler tells me. 

I did, however, learn that there is a ``Cow`` type in rust. That's pretty cool, I guess.

I gave up at this point. I don't like rust.


Go
--

Go is weird. I don't like the capital letters for method names convention. Regardless, the ``switch`` statement wasn't particularly difficult to get up and running by now.


.. code-block:: go 

    func main() {
        if (len(os.Args) > 1) {
            switch os.Args[1] {
                case "stat":
                    stat();
                    return;
                case "branch":
                    fmt.Println(getBranchName());
                    return;
                case "cm":
                    if (len(os.Args) > 2) {
                        cm(os.Args[2:]);
                        return;
                    }
                default:
                    usage();
                    return;
            }
        } else {
            usage();
            return;
        }
    }
    
Getting the branch name proved fairly trivial, so I thought Go was looking good.


.. code-block:: go

    func getBranchName() string {
        out, err := exec.Command("git", "status").Output();
        if (err != nil) {
            log.Fatal(err);
        }

        lines := strings.Split(string(out), "\n");
        pieces := strings.Split(lines[0], " ");
        if (len(pieces) > 0) {
            return pieces[len(pieces)-1];
        } else {
            return ""
        }
    }
        

Again, ``stat`` and ``origin`` were quite easy to implement:


.. code-block:: go 

    func stat() {
        out, err := exec.Command("git", "status").Output();
        if (err != nil) {
            log.Fatal(err);
        }
        fmt.Printf("%s", out);
    }
    
    func origin() {
        branch := getBranchName();
        out, err := exec.Command("git", "push", "origin", branch).Output();

        if (err != nil) {
            log.Fatal(err);
        }
        fmt.Printf("%s", out);
    }
    
Trying to implement ``cm`` however, had me stumped. I couldn't get it to recognise the ``-m`` flag with the speech marks, and there is NO helpful output at all, just "exit code 1";

My colleague who loves Go eventually helped me get ``cm`` working. It turned out that the commit message needed to be a single argument:


.. code-block:: go

  func cm(args []string) {
    var message_args []string;
    args[0] = "'" + args[0];
    for _, arg := range args {
        message_args = append(message_args, arg);
    }
    message_args[len(message_args)-1] = message_args[len(message_args)-1] + "'";
    full_cmd := strings.Join(message_args, " ");
    cmd := exec.Command("git","commit", "-m", full_cmd);
    out, err := cmd.Output();
    if (err != nil) {
        fmt.Println("error");
        log.Fatal(err);
    }
    fmt.Printf("%s", out);
  } 
            

That's it. I have now written (or at least, attempted to write) a git helper replacement in five compiled languages. 

Final Thoughts
--------------

Nim and D are both fantastic. If I actually have a reason to use a compiled language for myself, I will be using one of them (probably Nim). 

If I need to do something which involves input from other people, I think I would be happy to go with C++. It was a strange experience, but I was still able to get the program built in a fairly small amount of time due to the wealth of existing information about it.

Now I just need to think of a reason to use a new language!

