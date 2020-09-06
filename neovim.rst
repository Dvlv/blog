:Title: Update on using (neo)vim for python development
:Date: 2020-08-08
:Tags: Linux Development
:Category: Linux

A while back I wrote a blog post about switching from JetBrains IDEs to vim. In that blog post I
explained my rationale and went over my ``vimrc`` file. 

Almost a year later, and I'm writing another blog post as I've switched editors again - sort of.

From Vim to Neovim
==================

So, why the switch? Mainly for one specific Plugin - `Conquer of Completion <https://github.com/neoclide/coc.nvim>`_

This plugin describes itself as "Intellisense for Neovim". It provides a myriad of useful
features, but mainly autocomplete popups, and seamless integration with other fantastic addons
to a text editor, such as emmet and jedi.

While the ``.vimrc`` file isn't *quite* just a copy-paste job to get neovim up to speed, enough
of it was transferrable to make the switch pretty painless (especially compared to the JetBrains -> Vim switch).

My new Init.vim
===============

As I went over my ``vimrc`` file in the last post, I suppose I should go over my ``init.vim`` in this post.

A lot of it ported straight over, so I'll mostly be going over the plugins I have to make neovim a pleasant
IDE experience.

.. code-block:: vim

    Plug 'neoclide/coc.nvim', {'branch': 'release'}
    Plug 'Shougo/deoplete.nvim', { 'do': ':UpdateRemotePlugins' }
    Plug 'deoplete-plugins/deoplete-jedi'


First off, the aforementioned Coc, which enables the use of a language-server background, as well
as linting and emmet support. Along with Coc comes Jedi, a must-have for python development. It acts
as a language server and offers the ability to jump around the codebase and view definitions for functions
as I write. 

.. code-block:: vim

    Plug 'jaxbot/semantic-highlight.vim'
    Plug 'cloudhead/neovim-fuzzy'
    Plug 'mileszs/ack.vim'


This set of plugins carries over functionality which I got used to from my days using KDevelop to write PHP.

Semantic highlighting was talked about in my previous post, and is something I cannot code without
anymore. 

Fuzzy allows me to bring up a list of all files in the project folder with a shortcut - Ctrl+Q - and
then do a fuzzy search. This allows me to quickly open a file, say ``web/admin/templates/products/product_search_form.html``
by simply hitting Ctrl+Q and typing "te/prosea". Functionality like this is invaluable for navigating
quickly around a project.

Ack, as mentioned before, is like a project-wide find.

.. code-block:: vim

    Plug 'ObserverOfTime/coloresque.vim'
    Plug 'tpope/vim-surround'
    Plug 'unblevable/quick-scope'


Coloresque highlights colour strings (like "blue" or "#ff6600") with their colour, so that you can see
at a glance what they are referring to.

Surround allows me to surround some amount of text with quotes, brackets or html tags.

Quickscope is something I don't get a huge amount of use from, but it is sometimes nice. It will highlight
a character in all words on the cursor's current line with the quickest way to jump to that word.
It's a bit hard to explain in text, so check out `the repo <https://github.com/unblevable/quick-scope>`_ for
a graphical demonstration.

.. code-block:: vim

    Plug 'pangloss/vim-javascript'
    Plug 'tpope/vim-fugitive'

Vim-javascript provides nicer syntax highlighting abilities for javascript files. Not much to explain there.

Fugitive is a git integration which seems rather nice on paper. The problem is I'm much too used to quitting
my editor and using git from the command line, so I don't use it as much as I would think I should.

Summary of my experience
========================

Now that I have the power of intellisense in neovim, I don't think there's anything at all that I could
do with Pycharm, or any other graphical IDE, which I cannot find a way to do in neovim.

If anyone is considering trying out vim, either to just learn the movement or as a way of abandoning
a graphical IDE which takes like 10 minutes to start up and become usable, I would definitely say
that Neovim and CoC is a perfectly-viable development experience for a web developer using mainly
python.

If you are using Pycharm at the moment, and want to ease the transition to vim, there's a plugin
called IDEAVim which will allow you to use a subset of vim's movement controls within JetBrains IDEs.

Dotfiles
========

You can find my ``init.vim`` along with a bunch of other dotfiles `over on github <https://github.com/Dvlv/dotfiles>`_

