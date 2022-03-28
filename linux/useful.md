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
