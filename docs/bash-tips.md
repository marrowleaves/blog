---
title: Bash Tips
date: 2015-01-20 17:09:12
---

I've been learning Linux recently and this post is to argue against some points from [this post](https://www.ibm.com/developerworks/cn/aix/library/0811_yangbh_emacs2/)

The article is about 'why you should using shell inside emacs'. As a vim user I'd think this is some kind of abusing usage of emacs in the 'emacs-as-an-operating-system' style.

## Edit commands in an editor

The first thing the author mentioned is to editing current command in emacs and then execute it directly.

Actually you can use `C-x C-e` to invoke an editor to edit the current command. The editor chosen will be `$VISUAL || $EDITOR || emacs`.

see [edit-and-execute-command](https://www.gnu.org/software/bash/manual/html_node/Miscellaneous-Commands.html#index-edit_002dand_002dexecute_002dcommand-_0028C_002dx-C_002de_0029)

`C-x C-e` is also the shortcut for 'eval-last-sexp' in emacs. 

see [Lisp Eval](https://www.gnu.org/software/emacs/manual/html_node/emacs/Lisp-Eval.html)

## Edit history commands

The second benefit the author mentioned is to editing history commands.

Actually there's a built-in bash command `fc`, which allows editing history commands and re-execute them. The editor will be `$FCEDIT || $EDITOR || vi`.

see [Bash History Builtins](https://www.gnu.org/software/bash/manual/html_node/Bash-History-Builtins.html)

## Readline Mode

We can notice that some bash shortcuts are actually emacs shortcuts, like:
`C-b`, `C-f`, `C-p`, `C-n`, `M-b`, `M-f`, `C-r`, `C-a`, `C-e`.

see [emacs keybinds](http://www.cs.colostate.edu/helpdocs/emacs-bindings)

This is because bash is default in `emacs` mode.

see [Command Line Editing](https://www.gnu.org/software/bash/manual/html_node/Command-Line-Editing.html#Command-Line-Editing)

Use `set -o vi` to change this behaviour.
And in `vi` mode, press `v` to enter visual mode vi.

## Interesting Key Interpertation in Emacs under Windows

I noticed that to execute `Ctrl-Meta` commands on my Windows box, these combinations will work: `CTRL_L+ALT_L`, `CTRL_R+ALT_R` and `CTRL_R+ALT_L`, but `CTRL_L+ALT_R` won't work.

From the [manual](http://www.gnu.org/software/emacs/manual/html_node/emacs/Windows-Keyboard.html), it says the combination `CTRL_L+ALT_R` is used to produce the `AltGr` key, even though my Sun Type 6 has a physical `AltGr` key
