:Title: Sweet Squish - Ludum Dare 44 Post Mortem
:Date: 2019-05-04
:Tags: Godot Jam
:Category: Godot

For the unfamiliar, Ludum Dare is a 48 or 72 hour game jam. The organisers announce a theme and begin a countdown, then you have either 48 hours to create a game by yourself and enter it into the "Compo", or 72 hours to work in a team of any size to enter a game into their "Jam".

This was my second attempt at a Ludum Dare compo. You can read about my first entry |overtime| 

.. |overtime| raw:: html

   <a href="https://www.dvlv.co.uk/overtime-ludum-dare-43-post-mortem.html" target="_blank">Overtime here.</a>


Day One
=======

The event kicked off at 2:00am Saturday. I was planning on getting up around 5am, as I do this on weekdays for work anyway. I ended up waking up naturally about 4:30, and decided to just go for it.

I rolled over and checked the theme - "Your Life is Currency".

Initial Reaction
----------------
I wasn't keen on this theme at all! There were much better options in the final voting round, including "Extremely overpowered" and "Underground" which I was hoping for.

My first thought was the play as a big greedy man who loves to hoard money - he considers it his life. You would go around selling everything, until you become homeless, then sell your children as well and realise you're a monster.

Coming up with actual gameplay around this idea was too hard, so I threw it away and tried to come up with something better.

As a break from thinking, I opened up Pokemon Shuffle on an old phone and started using up the lives I'd "earned" while asleep. 

My idea was then right in front of me.

Mobile match-3 games are basically perfect examples of this theme. You trade your life (as in, time) for in-game lives. 

I came up with the idea of a kid playing a match-3 mobile game, and runs out of lives. They then use a time machine to advance time in order to get more lives, but wasting away his own life in the process.

The gameplay would be the levels of a match-3 game, so this idea was solidified. I wrote up the idea in a text file around 5:30am.

I decided that my game should be a parody of what is probably the most popular match-3 mobile game - Candy Crush.

Beginning Development
---------------------
I decided to begin with making the match-3 game itself. I fired up GIMP and made some lovely coloured squares to act as placeholders for sweets.
I also mocked-up the frame of a mobile phone to act as the background.

Godot was then fired up and I began coding a very rough implementation of match-3 logic. The initial framework had a 3x3 grid in which sweets could be matched either vertically or horizontally and would disappear. Once all sweets disappeared, the level was considered won, and another screen would pop up saying "You Win".

By 7:30 this rough implementation was finished, and the small notebook laptop I was working on was in single-digit battery level, so I moved over to my proper setup at my desk, but not before setting up a repo on both Github and Bitbucket to save copies of my work.

I continued to tweak the logic of the match-3 game until around 9:05, when I stopped for breakfast and to gather some future thoughts.

By 10am I had a rough idea of how the story was going to go together, and I had implemented some of the boilerplate scenes for my godot projects, including my own cutscene system.

By 11am the game as a whole was technically "playable". There was still much tweaking to do, as it felt very short and easy, but I was confident that I would have something to submit even if I lost a good chunk of Sunday.

Since there were only a few ways to organise 3 sweet varieties on a 3x3 grid, I decided to spice it up a bit by creating a 4x3 grid and making some levels with 4-sweets-of-a-kind to match, and if I had time, I would add a 4th colour sweet.

This involved a new scene for the new board, and a big refactor to the matching logic I had already written, but it didn't prove too difficult.

By 12:15 I had finished, and decided to break for lunch.

Afternoon
---------
After lunch I decided to work on the aspect I left until last on my previous entry, and was quite disappointed with - Sounds.

I made sure to find a few tools which I could use before the jam this time around:

|chrome|

|leshy|

.. |chrome| raw:: html

   <a href="https://musiclab.chromeexperiments.com/Song-Maker/" target="_blank">Chrome Music Lab.</a>
   
.. |leshy| raw:: html

   <a href="https://www.leshylabs.com/apps/sfMaker/" target="_blank">Leshy SFMaker.</a>

I made a cheerful background tune for playing the mobile game (which I had now decided to name Sweet Squish) and a more dull sounding background tune for the "real world" representation.

I also created some small jingles for when the player wins, and when they are out of Tickets (Sweet Squish's equivalent of "lives" or "hearts" in your standard mobile game). 

After an hour or so of creating music, I decided to also make a start on the artwork - another feature I had left until late on Sunday last time.

I drew the three types of sweets - an orange jelly bean, a pink hard candy, and a yellow gummy bear. I then drew a happy sky background, and made the phone border a bit nicer.

I combined the three types of sweet into a logo similar to Candy Crush's, then worked on something which would indicate the player's moves better. 

After a couple more hours, I stopped for dinner around 5:00.

This was it for the day. I was very happy with the progress I had made compared to day 1 of last jam, so I decided to take it easy for the evening.

Last time I continued well into the evening, and found it very difficult to get motivated Sunday morning. I wanted to avoid that feeling this time.

Day Two
=======
I awoke close to 6am, and decided I would begin work on the hour.

Finishing off art
-----------------
My biggest art pieces would be the bedroom scene and the time machine interior. I began with the bedroom and, to my surprise, it wasn't as terrible as I had thought. I'm not really happy with how the time machine looks, but it's not like I have a real-world example to go off of!

The time machine interior just ended up being a couple of panels of switches and buttons, and I decided to change some colours for a second "frame" which would show after the player has chosen how far to go forward in the future.

With the main art all done, I decided to put off writing and finishing the story implementation to do some animations while I was still in the mood. I made the squishing animation for matched sweets (more a "shrinking" animation really, but shhh!) as well as some bouncy animations for the logo and "You Win" graphics.

Art then had to be drawn for all of the ending screens. I decided to just draw one screen per ending, so I wouldn't need to spend the *entire* day making art.

Riding the tail end of my creative mood, I added a couple more sound effects to the game. One for selecting a sweet, and one for squishing them. The dialogue system still needed its trademark sound-per-letter, and I somehow managed to generate a sound which didn't sound too robotic or annoying.

Finally, some distinct background music for when the player wins was made. 

Last thing to do was implement the endings and tweak the story and mechanics to my liking.

At 1:30 I stopped to eat. 

Afternoon
---------
I considered the game pretty much finished when I went for lunch, so post-lunch work was all about polish. I added a fourth sweet type and created a bunch more levels. I finalised the amount of levels the player would need to pass in order to win, and made sure that there was a unique level for each. 

Since the first two levels only required clearing two types of sweets, they did not need a 3x3 grid. I decided I had time to make a 2x3 and 3x2 grid for these levels, which was easily achieved now that I had split out logic for the 4x3 grid. 

After about 20 "final" runthroughs of the whole game, I decided to call it finished and get it exported. 

Submission
----------
While exporting Godot games is typically incredibly painless, for some reason the game was *refusing* to run on itch.io - a site which hosts games and is very popular with Ludum Dare entrants.

I set up my game on itch and uploaded the files, then headed on over to the game's page and clicked "Run Game". A bar loaded, then said "Abort(114)". Looked like my game was broken, but the ``.html`` file Godot had generated was working fine.

After trying about 8 times with different solutions found online, I used my leet haxxor skills as a web developer and took to Inspect Element to see what was going on. I saw a message inside the ``iframe`` which hosts each game and saw "browser not compatible". Hmm.

I was using Firefox 66 on Arch linux to make the game, and it seems this isn't compatible with Itch.io's embedded games. LAME!

I switched back to my old netbook, which has Chromium installed, and checked there. The game was working! The audio was stuttery, but it ran. 

I booted up my gaming laptop with Windows 10 and checked it in Firefox Nightly 68. It worked fine. I decided that this was good enough, and I would just have to leave a note on the Ludum Dare page that the itch version didn't work on linux Firefox. 

Speaking of which, I wrote up the submission details on the ldjam site, created a Github release for the exported html soure, and finished my submission at about 3:40pm. 

Can I Play It?
--------------

If you want to, you can `play Sweet Squish here on itch.io. <https://dvlv.itch.io/sweet-squish>`_ 

You can also `view the entry on ldjam here. <https://ldjam.com/events/ludum-dare/44/sweet-squish>`_ 

As per the rules (I would have done it anyway!) the source code is also available `over on my Github. <https://github.com/Dvlv/ld44>`_





