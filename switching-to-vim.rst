:Title: Switching from JetBrains IDEs to Vim
:Date: 2019-08-29
:Tags: Linux
:Category: Linux

The JetBrains IDEs are the industry standard when it comes to writing code - and for very good reason. It's hard to deny that they're probably 
the best IDEs out there. An easy excuse to not try them out was their lack of semantic highlighting, but they eventually added that in 2017, so 
I had to give them a go. Of course, I was very impressed, and I continued to use them for a year or so ("them" being PHPStorm and PyCharm).

So, if I like them, why would I switch to something else? Firstly, I became annoyed by their ridiculous start-up time. Not only does it take a minute 
or so to go from launching the application to being able to open a project, it then takes *even longer* for the indexer to run and the IDE to 
become fully functional.

As well as this, there's obviously the licensing. I prefer to use free software wherever possible. While there is a "Community Version" of 
PyCharm, there is not an equivalent for ALL of their products. Not only this, but the community version of PyCharm is missing crucial parts of 
web development such as HTML and Sass colouring or Jinja support. 

I've been using the IDEAVim plugin in all editors for a good while now, so I figured I'd dive all the way in and see if I can make Vim a proper 
replacement for PyCharm and PHPStorm. In this post I'll go over the plugins I use to get IDE-esque features out of plain Vim.

SemanticHighlight
-----------------

There is no question - any editor I use *HAS* to have semantic highlighting. Since discovering it in KDevelop, and initially trying to turn it 
off, I now cannot live without it. Anyone reading this who hasn't experienced programming with semantic highlighting, I urge you to give it a try 
if your editor-of-choice supports it. 

Luckily, there's a plugin for Vim which does it... quite well. It's not perfect, and despite having around 30 available colours, I still end up with 
a lot of repeats within the same function / class. 

Editing normal text in Vim becomes interesting when using a semantic highlighting plugin. To alleviate this, I *could* only enable it in my language 
configuration files, but I decided I'd rather torture myself and have it on an exclude basis rather than include.

Relevant vimrc
==============

.. code-block:: vim

    cnoreabbrev sem SemanticHighlight
    cnoreabbrev semr SemanticHighlightRevert


    let g:semanticTermColors = [1, 3, 5, 6, 17, 30, 34, 54, 64, 88, 91, 98, 100, 107, 125, 129, 136, 142, 145, 148, 166, 170, 181, 202, 205]
    let g:semanticGUIColors = ["#800000", "#808000", "#800080","#008080", "#00005f", "#008787", "#00af00", "#5f0087", "#5f8700", "#870000", "#8700af",
                \ "#875fd7", "#878700", "#87af5f", "#af005f", "#af00ff", "#af8700", "#afaf00", "#afafaf", "#afd700", "#d75f00", "#d75fd7", 
                            \ "#d7afaf", "#ff5f00", "#ff5faf", "#ff6600", "#705598", "#6da741"]

    autocmd! bufwritepost .vimrc source %
    autocmd VimEnter *.md,*.rst,*.txt,*.html,*.jinja let b:dontsem=1

    fun! MaybeSem()
        if exists('b:dontsem')
            return
        endif
        if &ft =~ 'markdown\|html\|text'
            return
        endif
        SemanticHighlight
    endfun

    autocmd VimEnter * call MaybeSem()
    autocmd InsertLeave * call MaybeSem()


Ctrl-P
------

KDevelop's Quick Open feature is amazing, and JetBrains IDEs offer something similar which I don't know the name of off-hand. Pressing a 
shortcut (Ctrl-Q in my case) presents a list of files in the project, and they can be fuzzy-filtered and opened without taking hands off 
of the keyboard. Ctrl-P Vim offers this. 

There is a tradeoff here though, instead of waiting 5 minutes for PyCharm to index on startup, Ctrl-P will index when you first invoke it, meaning 
if you do not open the file you want initially, you may have to wait ~30 seconds to search for it. 

Also, it comes with a default limit of files to index, which I didn't realise until I got put on a Laravel Project created by someone with no 
respect for harddrive space, and wondered why I couldn't quick open a file which I knew existed.

Relevant vimrc
==============

.. code-block:: vim

    let g:ctrlp_map = '<C-q>'
    let g:ctrlp_path_nolim=1
    let g:ctrlp_path_sort=1
    let g:ctrlp_max_files=0 


Ack
---

I've mentioned ack in `a previous post about Flask. <https://www.dvlv.co.uk/how-to-get-flask-to-auto-restart-despite-syntax-errors.html>`_ Ack is somewhat like an alternative to grep. This allows me to do a "Find All" throughout a 
project in vim.

`My .ackrc can be found here. <https://github.com/Dvlv/dotfiles/blob/master/ackrc>`_

Relevant vimrc
==============

.. code-block:: vim 

    map <C-f> :LAck! -Q ""<left>


ALE
---

ALE is the asynchronous linting engine for vim. I only have this enabled for python projects, and it's great at spotting silly mistakes before I 
bother trying to run the code. 

On top of that, one of our projects requires running `black <https://github.com/psf/black>`_ over the code before checking it in. ALE can be 
used to run black quietly on every save and overwrite the file while it is open. Pretty neat.

Relevant vimrc
==============

TODO: check if work laptop vimrc has different stuff

.. code-block:: vim

    let g:ale_lint_on_insert_leave=1
    let g:ale_fixers = ["black"]
    let g:ale_fix_on_save=1


Airline
-------

Not a critical part of my setup, but it nicely integrates with ALE to show existing errors and shows "tabs" of which files I have open.

Relevant vimrc
==============

.. code-block:: vim 

    let g:airline#extensions#ale#enabled = 1
    let g:airline#extensions#tabline#enabled = 1
    let g:airline_theme='papercolor'


Other miscellaneous vimrc goodies
---------------------------------

- ``set showmatch`` - Shows matching brackets.

- ``set smartcase`` - When searching, will be case insensitive unless you type a capital.

- ``map <C-b> :buffers<CR>`` - Pressing Control + b shows which files I have open.

- ``set listchars=tab:>.,trail:.,extends:#,nbsp:.`` - combine this with ``set list`` to show visible whitespace.

- ``set tabstop=8 softtabstop=0 expandtab shiftwidth=4 smarttab`` - Tab key inserts 4 spaces.

- ``set relativenumber`` - Relative line numbers, makes doing ``4j`` and such easier.

- ``set hidden`` - Allows you to switch buffer without saving first.



My vimrc
--------

You can grab my vimrc `from Github here <https://github.com/Dvlv/dotfiles/blob/master/vimrc>`_ if you so desire, though most of it is available here.

I use `pathogen <https://github.com/tpope/vim-pathogen>`_ to manage plugins. 
