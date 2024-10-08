# Offensive Win

Sometimes you will not have a choice, and will have to use windows for your pentest.  
Some great tools and resources have been done for this purpose and some other tools are multi platform and can be used on windows.

## WSL

If you are able to virtualize, you can use WSL.  
It is way simpler to install than it used to be.  
Launch cmd as admin and run `wsl --install` this will also install the ubuntu distribution.  
If you want kali you can also have it.  
You can follow the instructions from [here](https://www.kali.org/docs/wsl/wsl-preparations/)  
But basically with ubuntu you should be able to install anything you wish.

## CommandoVM

Commando VM is a windows 10 vm made by mandiant for offensive security.

- Get it [here](https://github.com/mandiant/commando-vm)

## Install Impacket on windows

### You will need

- Microsoft C++ build tools. You can get it [here](https://visualstudio.microsoft.com/visual-cpp-build-tools/)
- rust compiler. You can get rust [here](https://www.rust-lang.org/tools/install)
- Python, you can get it [here](https://www.python.org/downloads/windows/). If you need some assistance, [this documentation](https://www.digitalocean.com/community/tutorials/install-python-windows-10) from digital ocean is really good.
- Finally you should be able to install Impacket `python -m pip install impacket`
- Once installed impacktet should be in `C:\User\Username\AppData\Local\Programs\Python\Python312\Lib\site-packages\impacket`

## Resources

- [Kali-windows-binaries](https://github.com/interference-security/kali-windows-binaries)