Title: Improving Fedora Silverblue's Toolbox Prompt
Date: 2022-05-07
Tags: Linux
Category: Linux

Fedora Silverblue uses a read-only `/usr` folder, meaning software should be installed via other means. One of those means is via Podman containers using a tool called Toolbox (which is being renamed Toolbx).

When you enter one of these Toolbox containers, your prompt gains a small pink dot and the Hostname changes to "toolbox". This effect is, in my opinion, much too subtle.

<img src="{static}/images/toolboxblog/1.png" alt="Default Toolbox Prompt" width="50%">

Let's remedy this by editing the `PS1` prompt in `.bashrc`. TL;DR look at my .bashrc here: [Github Dotfiles](https://github.com/Dvlv/dotfiles/blob/master/.bashrc)

### Adding Some Colour and Flair

Let's add a bit of colour, and some shapes, around the PS1:

```bash
export PS1="\[\033[1;37m\]<\u|\[\033[32m\]\w\[\033[97m\]>\[\033[0m\] "
```

This wraps the PS1 in angled brackets, replaces the hostname with the current working directory, sets the main colour to white and the current directory to green.

<img src="{static}/images/toolboxblog/2.png" alt="Style" width="50%">


Great, much better!

### Toolbox Indication

Now we'll need this to change when we enter a Toolbox. As a Millenial, I'll use an emoji to indicate this, and also change the colour of the prompt from white to brown when inside a Toolbox.

We can check if we're in a Toolbox by either reading the hostname, or checking for the metadata file `/run/.containerenv`, then echo a different emoji:

```bash
toolbox_or_not() {
    HN=$(hostname)
    if [ $HN = "toolbox" ]
    then
	    echo "🧰\[\033[38;5;094m\]"
    else
	    echo 💻
    fi
}

```

Now we'll add a call to this to our PS1:

```bash
export PS1="\[\033[1;37m\]<$(toolbox_or_not):\u|\[\033[32m\]\w\[\033[97m\]>\[\033[0m\] "
```

Now there's an emoji and a colon before our username, and the Toolbox's prompt will be brown:

<img src="{static}/images/toolboxblog/3.png" alt="With Emoji" width="50%">

Now we just need to indicate which toolbox we are actually on. The hostname for all toolboxes is just "toolbox", so that's no help. Luckily we have that `/run/.containerenv` file:


```bash
get_toolbox_name() {
	awk 'match($0, /name=/) {print $0}' /run/.containerenv | awk -F '"' '$0=$2'
}
```

Now we can get a _second_ emoji to represent the Toolbox's name:

```bash
get_toolbox_emoji() {
	TBNAME=$(get_toolbox_name)
	case $TBNAME in

		brave)
			echo 🦁
			;;

		chrome)
			echo ⚽
			;;

		py)
			echo 🐍
			;;
			
		*)
			echo 🤷
			;;
	esac
}
```

And we just add this function to our `toolbox_or_not` like so:


```bash
toolbox_or_not() {
    HN=$(hostname)
    if [ $HN = "toolbox" ]
    then
	    echo "🧰$(get_toolbox_emoji)\[\033[38;5;094m\]"
    else
	    echo 💻
    fi
}

```

Now we have a much clearer indicator that a terminal window is actually a Toolbox, and a visual indicator as to which Toolbox we are in!

<img src="{static}/images/toolboxblog/4.png" alt="Finished" width="50%">
