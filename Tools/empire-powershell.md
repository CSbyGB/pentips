# Powershell Empire & Starkiller

## What is this

- Powershell Empire is a C2 framework
- Starkiller is a GUI for Powershell Empire

## Installation on a Kali VM

- When I wanted to install it for a lab I was playing it on TryHackMe, the instuctions did not work on my end.
- So I decided to document here a stable install.
- This install is based on [this youtube video by Professor K](https://youtu.be/6eqrnZzffJ4).
- First make a snapshot of your VM prior to the install to be sure that you can roll back in case something goes wrong.
- `sudo su` make yourself root for this process
- `apt update && apt -y full-upgrade` update and upgrade your VM
- Make another snapshot of it afterwards.
- `sudo apt install -y powershell-empire starkiller` install powershell empire and starkiller

## Launch

- `sudo powershell-empire server` start the server
- `sudo powershell-empire client` start the client
- `starkiller` --no-sandbox
- Default creds are `empireadmin:password123`