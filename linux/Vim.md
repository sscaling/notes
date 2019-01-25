Vim Cheatsheet
==============

General
-------

```
<n><cmd>               # repeat command n times
<cmd><n><selector>     # repeat command n-times for selector, e.g. d5w - delete 5 words
:r <filename>          # read <filename> into current position
:r! <cmd>              #
:w <filename>          # write file (or current visual selection) to filename
:sh[ell]
```

Navigation
----------

```
    k^
< h   l >
    jv  (j kinda looks like a down arrow, h + l are to the left + right)

0|^ / $ - start / end of line
w - next start of word
e - end of word
b - beginning of word
% - match brackets
/ - forward search
? - backward search
n / N - next / previous match
gg / G - start / end of file

CTRL-F / CTRL-B # jump page forward / backwards

* - search forward for identifier under cursor
# - search backwards

:marks      # print marks list
:ju[mps]        # print jumps list
CTRL-]          # Enter / Select
CTRL-O / CTRL-I # jumpt to older / newer position in jump list
CTRL-W CTRL-W - jump between windows
```

Text Editing
------------

```
R - replace mode. Overwrite text
a / A - insert mode after cursor / line
o / O - insert new line in insert mode after / before current line
x - delete current character
d<selector> - delete selection, i.e. dw
dd - delete line

s/foo/bar[/g]     # replace foo with bar on line
n,ms/foo/bar      # replace foo with bar on lines n to m
%s/foo/bar        # replace foo with bar on each line of file

y                 # yank (copy) text
Y / yy            # yank lines
p / P             # paste text after / before cursor
[ / ]             # goto start / end of previous put text

:reg              # list registers
"{char} p|y       # Use register for next paste / yank
```

Windows
-------

```
:help windows-intro
```

-	buffer == file
-	window is a viewport of a buffer
-	tab collection of windows

Uses CTRL-W followed by options to manipulate windows

```
:buff            # List buffers
:bn / bp         # next / previous buffer
:b <n>           # Switch to buffer <n>
sp / new         # Split current / new buffer in a horizontal window
vs / vnew        # as above in vertical window
CTRL-W CTRL-O    # Make current window the only one on screen
```

Code
----

```
[( / [{     # move backwards to unmatched bracket
]( / ]{     # move forwards to umatched bracket

gd          # search for local declaration
gD          # search for global declaration
[?]if       # inner function
[?]of       # outer function
[?]af       # A function
```

Quickfix
--------

```
:cnext      # Move to next error
:cprev      # previous error
:cclose     # Close quick fix
```

Configuration
-------------

*NOTE*: Prepend 'no to switch off an option. i.e. noic

```
:set hls      # enable highlight search (alias: hlsearch)
:set ruler    # enable ruler at bottom of page
:set number   # enable line numbers
:set ic       # ignore case (alias: ignorecase)
:set is       # increment search (alias: incsearch)

:set autowrite  # write contents of file automatically when :make is called
```

Tabs
----

[http://tedlogan.com/techblog3.html](http://tedlogan.com/techblog3.html)

examples:

```
:set softtabstop=4 shiftwidth=4 expandtab
:set tabstop=4 softtabstop=4 shiftwidth=4 noexpandtab
```

-	tabstop - how many columns a tab counts for.
-	expandtab / noexpandtab - if set, using Tab in insert mode will produce appropriate number of spaces
-	shiftwidth - how many columns text is indented with the re-indent operations `\<\< and \>\>`
-	softtabstop - control how many columns are used when tab is used in insert mode. if softtabstop is less-than tabstop and expandtab is not set, vim will use a combination of tabs and space to make up the desired spacing. if softtabstop equals tabstop and expandtab is not set, vim will always use tabs. When expandtab is set, vim will always use the appropriate number of spaces.

*NOTE*: enable visual markers such as

```
:set list
```

Spelling
--------

```
:set spell spelllang=en_us
]s / [s     - move forward / back through spelling mistakes
z=          - show options
```

Extras
------

-	[Modeline](http://vim.wikia.com/wiki/Modeline_magic) - Specify a comment at top/bottom of document to apply vim specific settings
