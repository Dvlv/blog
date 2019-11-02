:Title: PlainMan - Ludum Dare 45 Post Mortem
:Date: 2019-10-13
:Tags: Godot Jam
:Category: Godot

For the unfamiliar, Ludum Dare is a 48 or 72 hour game jam. The organisers announce a theme and begin a countdown, then you have either 48 hours to create a game by yourself and enter it into the "Compo", or 72 hours to work in a team of any size to enter a game into their "Jam".

This was my third attempt at a Ludum Dare compo.


Day One
=======

The event kicked off at 11pm Friday. Previous events had been 2am Saturday - 2am Monday, but they seem to have moved it 3 hours earlier now. 
It made no difference to me though, I can't stay awake much past 10pm anyway!

I awoke sometime around 6:15 Saturday and checked the theme - "Start With Nothing".

Initial Reaction
----------------
This theme wasn't *bad* but it wasn't exactly helpful either. I think it can be retro-fitted to large amount of existing games, so it's not great for dealing 
with 'blank page syndrome'.

The idea which came to my mind almost immediately was the old metroid games. You would often have all of your different weapons and abilities at the very start, but 
five minutes or so into the game, they would all disappear, and you would run around the world re-collecting them.

This brought out the idea of some kind of character with powers who loses them, or perhaps somebody with no powers who has to earn them. 

That was the idea I stuck with for my game - a superhero with nothing who has to accrue some superpowers.

My initial idea ended up with four minigames which each earned you a specific power, followed by a boss fight at the end once you were strong enough to fight. 
The ideas were quickly culled down to just three games, one granting you the ability to become super, and the other two granting the powers of jumping and strength.

I hadn't fully decided how each game would work, but I was eager to get started.

Beginning Development
---------------------
By just 6:45 I had a basic idea of what I needed to do to get going, and so I turned my laptop on and began building up the base project in Godot.

I imported in my usual Dialogue system from past games and began working on basic platforming capabilities for a character. 


In past attempts I'd left art until near the end, but I wanted to do my art in Inkscape this time, so I began early - around 7:55. I also ended giving up on Inkscape 
pretty early too, since drawing SVGs had quirks which I wasn't used to, and decided it would probably be best to stick with tools I know when on a deadline.

I stopped for breakfast around 9:15, then carried on doing artwork until around 10 when I fancied a bit more programming.

By 11:00 I had the ideas and basic logic for the two main minigames done. The game would take place around a farm at which the hero would need to work in order to 
gain powers. To gain super-jump, he would have to collect flying chickens by jumping to catch them. To earn super-strength he would have to pull an unruly horse back 
to its owner. The chicken game would be regular platforming, and the horse game would be played by the player mashing a certain key which changes every couple of seconds.

With these two ideas implemented in basic form I went back to making more artwork.

Afternoon
---------

Around 12:30 I stopped for lunch having finished more art and debating with myself how to award the final powers to the player. 
I wanted to implement some kind of puzzle section but was unsure how to make it more interesting than 'talk to this guy first, then this guy second'.

I had decided to make the player answer a series of riddles to unlock their potential. Maybe not the most interesting "puzzle" but it was easy to program, and 
didn't involve drawing another huge area for the player to explore.

By around 14:15 I'd drawn and implemented this riddle room (luckily it was similar in mechanics to part of a previous entry) and began work on the final boss fight.

I stopped working around 16:30 - earlier than usual. From previous attempts I knew that motivation was lacking the morning of the second day, so I thought if I 
stopped around the same time as I usually leave my day job I hopefully wouldn't feel too burned-out.


Day Two
=======
I awoke around 6:30 again, and began pretty much straight away.

I had the logic all done for the chicken minigame but no level, collisions or art. I began by drawing a scene for the game and putting in small obstacles for the 
player to jump onto. Once the level was detailed and traversable, I just had to add some chickens, and that game was finished after just an hour.

My next area of attention was the final boss fight. This was still largely unfinished. I had the character and attack artworks, and some untested classes, but no 
level for them to inhabit. It took me until around 10:15 to draw and code the boss fight level. With that done I stopped for breakfast.

I picked up again with sound effects. I returned to Chrome Music Lab for my background tunes, and tried recording some real-world sounds for a few sound effects. 
I relly have no ear for what makes good music, so I'm not expecting high audio ratings.

With sounds added I needed to finish writing and tying-together the story. I can't help but give my games multiple endings, so those were written and coded around 11:00.

Afternoon
---------

By 13:15 I was finally done with cutscenes and art. Now it was time for some polish. I added an animation to the horse minigame's Key icon, then remembered I 
still needed to make all of the fonts bigger. This is a simple but very time-consuming task! Luckily, once again previous entries use the same engine, so I have a 
pre-styled font to import into this project. 

Around 14:15 I relised the time, and that I hadn't eaten yet, so I stopped for lunch. 

Post lunch I gave the game a few complete playovers and reliased that it was effectively finished. I made an intro screen and a thank you screen then began packaging.


Submission
----------

This was probably the first time I'd exported a game and it was fine. Itch.io still doesn't want to work in Firefox on my linux machine, but it ran in Chromium 
and in Firefox on my Windows machine, so I figured people would be able to play it. 

I played through both endings on my Windows machine and spotted no spelling mistakes or anything, so I decided to just submit it. 

By just a bit over 4pm I had my submission up on the Ludum Dare site, and I was done. 


Can I Play It?
--------------

If you want to, you can `play PlainMan here on itch.io. <https://dvlv.itch.io/plainman>`_ 

You can also `view the entry on ldjam here. <https://ldjam.com/events/ludum-dare/45/PlainMan>`_ 

As per the rules (I would have done it anyway!) the source code is also available `over on my Github. <https://github.com/Dvlv/ld45>`_





