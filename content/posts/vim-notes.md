+++
author = "Daulton"
title = "Vim notes"
date = "2018-08-27"
description = "Notes on Vim commands"
tags = [
    "notes",
]
categories = [
    "notes",
]
+++

I am beginning to learn Vim, these notes are to help me remember commands and tips as I find useful things that will help me.
<!--more-->

One of the reasons I am interested in Vim right now is because Vi is a text editor that is on almost all Linux machines by default. So by learning vim, there is a lot of overlap that can help in the future indirectly for when I am on a machine with only Vi. This is helpful as when you SSH into a server as an example you may not have been granted the required access to install packages, or you may not be allowed to install your own packages either. So knowing the tools available by default is optimal.

But overall, learning Vim has been on my to do list for a very long time since its so neat.

### Movement

1. 0 to to first charcter of a line 2. $ to go to last character of a line 3. Ctrl + f or b to go forward (down) or back (up) a page 4. 5G to jump to the fifth line

### Editing

1. 5> will input a tab starting on the current line for the entered amount of lines  
2. 5dd will delete the following 5 lines  
3. pressing 'u' will undo any edits, can be pressed repeatedly  
4. press 'del' key to delete current line under  
5. To replace the entire current line with a new line, press the c key twice in a  
row.

### Buffer

1. To open a file into a new window, type :sp filename.txt. filename.txt will appear open for editing in a new split window.
2. To switch between windows, type Ctrl+w+Ctrl+w (control-w twice). Any :q, :q!, :w and :x commands that you enter will only be applied to the currently-active window

### Visual mode

1. The best way to cut and paste is to use visual mode, you can also enter visual mode by hitting v
2. If you're copying the text, hit y (which stands for "yank"). If you're cutting the text, hit d. You'll be placed back in command mode. Now, move to the position where you'd like to insert the cut or copied text, and hit P to insert before the cursor, or p to insert after the cursor..

### Normal mode

1. yy or Y - yank the current line, including the newline character at the end of the line
2. p to paste after the cursor, or P to paste before the cursor

