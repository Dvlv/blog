:Title: Overtime - Ludum Dare 43 Post Mortem
:Date: 2018-12-10
:Tags: Godot
:Category: Godot

For the unfamiliar, Ludum Dare is a 48 or 72 hour game jam. The organisers announce a theme and begin a countdown, then you have either 48 hours to create a game
by yourself and enter it into the "Compo", or 72 hours to work in a team of any size to enter a game into their "Jam".

I have watched a few youtubers compete in the event, and decided to participate myself for round 43. Since I have no game-dev buddies I entered 
the Compo.


Day One
=======

The event kicked off at 2:00am Saturday, and the theme was "Sacrifices Must be Made".


Initial Reaction
----------------

I awoke at about 5:10am and had a look at the theme. I was happy to see that it was one of the two which I was hoping for (the other being "Don't Trust the Game") 
and appeared to offer a lot of flexibility.

My first thoughts were to do with a work-life balance idea, or some kind of event in which the player has to decide who to rescue from some sort of danger.

I had some thoughts about combining the two, but then decided that the latter may be a bit too dark a theme, so decided on some kind of work-simulation game (because work is super fun, right?).

Being unsure how to "simulate" working, I came to the idea of instead controlling how much work the player does via overtime. Using some sort of slider to 
set the amount of extra hours of overtime, thus earning more money, but taking away something else equally precious. 

Over a period of maybe 2 hours I had come up with a rough outline of a story which could be told using this formula for selecting overtime, and some branching
dialogue based on the choices made. I then took to Godot to check if there was a Slider I could use for the overtime mechanic.

There was, so the idea was confirmed.

Beginning Development
---------------------

I began by creating a scene in Godot which would allow the user to select a value with a Slider, then pass that over to some global variables. I would use a global
script to keep track of the amount of overtime worked and the amount of money earned. I also added in a couple of variables to track certain characters' "happiness"
but these would end up unused.

.. image:: {filename}/images/godot/ld43-overtime-amount.png
  :alt: overtime scene
  :width: 90%
  :align: center

I initially set up space for three characters to submit overtime, but that was stripped down to two later.

The next thing to do was to make a dialogue system. Luckily, I have |cutscene| so I was able to re-implement
this into my game.

.. |cutscene| raw:: html

   <a href="http://www.dvlv.co.uk/making-a-cutscene-with-text-dialogue-in-godot-30.html" target="_blank">already found a way to do that</a>


Once I had the dialogue in place I began painstakingly creating scenes which were used to display the branching script which I had drafted initially. There was no way yet to
change the image being displayed above the text, but I knew my dialogue system was flexible enough to allow me to very easily squeeze that in at a later point.

Afternoon rolled around and I had finished implementing all of the story. The next logical step was to combine it with the overtime choosing scene and have 
the sliders affect which route through the story the player takes. This wasn't too difficult at all thanks to the use of a global script, but I did have to
add another variable which took the amount of overtime worked immediately previous to the current scene (whereas before I only tracked total overtime).

With that, my "game" was technically finished. All it needed was the art and sound to go along with the story.

It was at that point I realised there was no gameplay other than a couple of sliders. Hardly a "game"!

Oops.

Adding Gameplay
---------------

As it was around 2:30pm, there was no way I could start again. I decided that the best course of action would be to implement some mini-games which are
in some way related to parts of the story and stick them in at various intervals. Since my story took place over five days, there was essentially one mini-game
per day, with Wednesday having two (since I had two ideas which would fit the narrative).

I took out a trusty notepad and began to sketch a few ideas (don't try to read my handwriting, even I can't read some of it):

.. image:: {filename}/images/godot/ld43-mg-1.png
  :alt: minigame ideas
  :width: 90%
  :align: center

First three ideas - Drive to work, Paper Sort, and Bag the Rubbish.

.. image:: {filename}/images/godot/ld43-mg-2.png
  :alt: minigame ideas
  :width: 90%
  :align: center

Two more - Find the Page and Memory.

The rest of the day involved creating the logic for these mini-games. I believe I started around 3:30pm and gave up around 7:10pm, with around 45 minutes to eat in between.

I first tackled Bag the Rubbish, then the Drive, followed by Paper Sort. I got tired and turned in for the day with Paper Sort around 80% completed. 

Of course, all of these games had coloured squares as placeholder art for now, since I hadn't even began to think about how I would style the art for this game.


Day Two
=======

I awoke around the same time on Sunday but wasn't as eager to jump straight in. After around an hour of procrastination I finally forced myself to get serious.

Finishing off Programming
-------------------------

My first objective was to create the Memory mini-game, since it could be done entirely with the dialogue system.

I then created the Find the Right Page game, which got renamed to BuildBid to fit the story a bit more.

With all of the mini-games created it came time to properly slot them into the existing scenes, which took more time than I had realised.

The next goal to tackle was the ability to show and change images in the dialogues. I had a pretty good idea of how to implement this, since the whole system
just relies on a single signal being emitted.

With the image system in place I now had to tackle the art itself. 

Art
---

Now, digital art has never been a strong point of mine. Neither, really, has physical art, although I have always enjoyed drawing. 

Somehow I came to the idea that I should use coloured markers on plain paper and actually draw the graphics for my game by hand. I gave it a try for a couple
of characters and it seemed like a good niche, so I went with it. 

I ended up with five sheets of paper full of art:

.. image:: {filename}/images/godot/ld43-art-1.png
  :alt: art
  :width: 90%
  :align: center

Some Art.

.. image:: {filename}/images/godot/ld43-art-2.png
  :alt: art
  :width: 90%
  :align: center

Some more art.

This art needed to get from paper to my computer, but we don't own a scanner. Luckily, I had an android app called CamScanner on my phone, which to my surprise
allowed for scanning in colour, too. The app decided that some of the sheets were actually landscape, even though I photographed them all portrait. I didn't 
pay it much mind though, opting to just rotate them in Gimp rather than mess about scanning and sending them again.

The artwork slowly trickled into various scenes, then I realised that the whole game needed a bit of a papery aesthetic to it, so I changed Godot's 
default background colour to white and updated all of my text to be black. I also took the opportunity to adjust the font, since I needed to import
a custom font in order to increase the size. 

I chose the Ubuntu font, since it's both one of my favourite fonts, and liberally licensed.

It then came time to create a beginning and ending screen, which convention dictates should include a logo for the game. Thing is, I hadn't drawn one, or even
came up with a name for the game. I very creatively decided to name the game "Overtime", since it was about... doing overtime. I then drew up the logo on 
a bit of space on one of the original sheets of paper and scanned that in, too.

With the beginning and ending scenes in place, the game was kinda done. The clock showed about 2:30pm, so I stopped to eat and consider what more I wanted to 
tackle before submission.

Sound
-----

Much like art, music was never really my thing either. I don't own any instruments or audio equipment in general (bar a headset) and I always found the online 
music creation tools to be awkward. I decided against adding any bgm to the game, but tried my hand at some sound effects.

Now, I'm using Arch Linux (btw) and at the time of writing it ships with Audacity v2.3, which the developers themselves consider "unstable" on Linux. 

Not only that, but it was a fairly fresh install, so many alsa-related libraries just weren't present.

After about 45 minutes of struggling to actually get sound out of Audacity, I somehow managed (I still don't know how, really) to get both speakers and microphone 
to work with my headset. 

I took the strategy I'd seem some youtubers employ with sounds, and just recorded myself making some noises with objects, then edited them a bit in audacity.

I got the sound of my keyboard keys being pressed (Cherry Blues, they're LOUD), altered the pitch, then sped it up and used it as the dialogue noise. 

Rustling a plastic bag got me some sound for Bag the Rubbish, and some noises from me flipping over pages of my notepad acted as sound effects for both 
Build Bid and Paper Sort.

The ambient noise of a driving car was something I couldn't quite get with everyday objects, so I left that and just used the sound of my hand smacking my desk 
as a sound for if the player crashes.

After those few, I was tired of Audacity crashing and deciding to not play sound, so I ceased to do any more sound work. 

I put the sounds into the scenes and gave the game a once-over with its new sounds. 

Some small text tweaks and font-size adjustments ended up as the final adjustments at 4:02pm.

I packaged the game up and submitted it to Ludum Dare probably around 5pm ish, where it now sits.

Can I Play It?
--------------

If you want to, you can `play overtime here on itch.io. <https://dvlv.itch.io/overtime>`_ 

You can also `view the entry on ldjam here. <https://ldjam.com/events/ludum-dare/43/overtime>`_ 

As per the rules (I would have done it anyway!) the source code is also available `over on my Github. <https://github.com/Dvlv/ld43>`_




