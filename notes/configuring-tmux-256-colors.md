# Enabling 256 Color mode in tmux

I'll be the first to admit I don't know how this works or if I've even got this right. 

First, [this script](https://github.com/robertknight/konsole/blob/master/tests/color-spaces.pl) tests the 256 color capabilities of your terminal. 

To configure things:

tl;dr: Follow the instructions in [this guide](https://gist.github.com/bbqtd/a4ac060d6f6b9ea6fe3aabe735aa9d95). Some notes:
* That guide recommends getting the terminfo for tmux-256color from https://invisible-island.net/datafiles/current/terminfo.src.gz but there is also [this tmux issue](https://github.com/tmux/tmux/issues/2262) in which the tmux author suggests grabbing it from [this gist of his](https://gist.github.com/nicm/ea9cf3c93f22e0246ec858122d9abea1). I first grabbed it from the tmux author's gist but then grabbed it from invisible-island.net after.
* In my case, I could not get things to work when iTerm is configured to report the terminal type as "xterm-256color" (I'd get the error "missing or unsuitable terminal: xterm-256color"). But setting iTerm to report the terminal type as "xterm" seems to work (iTerm2 Preferences / Profiles / Terminal / Report terminal type)
* I put this in my .tmux.conf:
```
# Set terminal type
set -g default-terminal "tmux-256color"
set-option -sa terminal-overrides ',xterm:RGB'
```

