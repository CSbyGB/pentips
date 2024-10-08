# AV Evastion: Shellcode

> This documentation has been made using the box AV Evasion Shellcode from Tryhackme. You can work on it [here](https://tryhackme.com/room/avevasionshellcode)

## Portable Executable

### What is it?

Windows Executable file format, aka PE (Portable Executable), is a data structure that holds information necessary for files.  
![PE structure overview](../.res/2022-09-09-10-54-04.png)  

- `.text` stores the actual code of the program
- `.data` holds the initialized and defined variables
- `.bss` holds the uninitialized data (declared variables with no assigned values)
- `.rdata` contains the read-only data
- `.edata`: contains exportable objects and related table information
- `.idata` imported objects and related table information
- `.reloc` image relocation information
- `.rsrc` links external resources used by the program such as images, icons, embedded binaries, and manifest file, which has all information about program versions, authors, company, and copyright!

Example steps in which the Windows loader reads an executable binary and runs it as a process

1. Header sections: DOS, Windows, and optional headers are parsed to provide information about the EXE file. For example,

   - The magic number starts with "MZ," which tells the loader that this is an EXE file.
   - File Signatures
   - Whether the file is compiled for x86 or x64 CPU architecture.
   - Creation timestamp.

2. Parsing the section table details, such as

   - Number of Sections the file contains.

3. Mapping the file contents into memory based on

   - The EntryPoint address and the offset of the ImageBase.
   - RVA: Relative Virtual Address, Addresses related to Imagebase.

4. Imports, DLLs, and other objects are loaded into the memory.
5. The EntryPoint address is located and the main execution function runs.

### How to store shellcode in PE (Examples)

1. Defining the shellcode as a local variable within the main function will store it in the .TEXT PE section.
2. Defining the shellcode as a global variable will store it in the .Data section.
3. Another technique involves storing the shellcode as a raw binary in an icon image and linking it within the code, so in this case, it shows up in the .rsrc Data section.
4. We can add a custom data section to store the shellcode.

### PE-Bear

PE-Bear is a software which helps to check the PE structure: Headers, Sections, etc. PE-Bear provides a graphic user interface to show all relevant EXE details.

- We can get it [here](https://github.com/hasherezade/pe-bear-releases)

## Introduction to Shellcode

 Shellcode is a set of crafted machine code instructions that tell the vulnerable program to run additional functions and, in most cases, provide access to a system shell or create a reverse command shell.

Once the shellcode is injected into a process and executed by the vulnerable software or program, it modifies the code run flow to update registers and functions of the program to execute the attacker's code.

It is generally written in Assembly language and translated into hexadecimal opcodes (operational codes). Writing unique and custom shellcode helps in evading AV software significantly.  

Methodology:

1. First we write our code in assembly.  
   Here is an example provided by tryhackme

```asm
global _start

section .text
_start:
    jmp MESSAGE      ; 1) let's jump to MESSAGE

GOBACK:
    mov rax, 0x1
    mov rdi, 0x1
    pop rsi          ; 3) we are popping into `rsi`; now we have the
                     ; address of "THM, Rocks!\r\n"
    mov rdx, 0xd
    syscall

    mov rax, 0x3c
    mov rdi, 0x0
    syscall

MESSAGE:
    call GOBACK       ; 2) we are going back, since we used `call`, that means
                      ; the return address, which is, in this case, the address
                      ; of "THM, Rocks!\r\n", is pushed into the stack.
    db "THM, Rocks!", 0dh, 0ah
```

2. We compile and link the ASM code to create an x64 linux executable file and execute the program.

```bash
nasm -f elf64 thm.asm
ld thm.o -o thm
./thm
```

3. We extract the shellcode with the `objdump` command by dumping the .text section of the compiled binary.

```asm
objdump -d thm
```

4. We use `objcopy` to dump the `.text` section into a new file called `thm.text` in a binary format `objcopy -j .text -O binary thm thm.text`

5. The thm.text contains our shellcode in binary format, so to be able to use it, we will need to convert it to hex first. The xxd command has the -i option that will output the binary file in a C string directly `xxd -i thm.text`

6. To confirm that the extracted shellcode works as we expected, we can execute our shellcode and inject it into a C program.

```c
#include <stdio.h>

int main(int argc, char **argv) {
    unsigned char message[] = {
        0xeb, 0x1e, 0xb8, 0x01, 0x00, 0x00, 0x00, 0xbf, 0x01, 0x00, 0x00, 0x00,
        0x5e, 0xba, 0x0d, 0x00, 0x00, 0x00, 0x0f, 0x05, 0xb8, 0x3c, 0x00, 0x00,
        0x00, 0xbf, 0x00, 0x00, 0x00, 0x00, 0x0f, 0x05, 0xe8, 0xdd, 0xff, 0xff,
        0xff, 0x54, 0x48, 0x4d, 0x2c, 0x20, 0x52, 0x6f, 0x63, 0x6b, 0x73, 0x21,
        0x0d, 0x0a
    };
    
    (*(void(*)())message)();
    return 0;
}
```

1. Then, we compile `gcc -g -Wall -z execstack thm.c -o thmx` and we execute it `./thmx`

## Generate shellcode using Public Tools

- `msfvenom -a x86 --platform windows -p windows/exec cmd=calc.exe -f c` this shellcode generated with msfvenom will launch calc in the target.

### Shellcode injection

Hackers inject shellcode into a running or new thread and process using various techniques. Shellcode injection techniques modify the program's execution flow to update registers and functions of the program to execute the attacker's own code.

## AMSI Bypass

- [AMSI Bypass – How it works - mdsec](https://www.mdsec.co.uk/2018/06/exploring-powershell-amsi-and-logging-evasion/)
- [Amsi-Bypass-Powershell](https://github.com/S3cur3Th1sSh1t/Amsi-Bypass-Powershell)
- [Making AMSI Jump - Offensive Defence](https://offensivedefence.co.uk/posts/making-amsi-jump/)
- [The RIse and Fall of AMSI - Tal Liberman](https://i.blackhat.com/briefings/asia/2018/asia-18-Tal-Liberman-Documenting-the-Undocumented-The-Rise-and-Fall-of-AMSI.pdf)
- [OffensiveNim - AMSI Patch](https://github.com/byt3bl33d3r/OffensiveNim/blob/master/src/amsi_patch_bin.nim)
- [Hunting for AMSI bypasses- Wee-Jing Chung](https://blog.f-secure.com/hunting-for-amsi-bypasses/)
- [Using Reflection for AMSI Bypass - redteam cafe](https://www.redteam.cafe/red-team/powershell/using-reflection-for-amsi-bypass)
- [AMSI Fail](https://amsi.fail/)
- [Memory Patching AMSI Bypass ](https://rastamouse.me/blog/asb-bypass-pt2/)
- [How to bypass AMSI and execute ANY malicious Powershell code](https://0x00-0x00.github.io/research/2018/10/28/How-to-bypass-AMSI-and-Execute-ANY-malicious-powershell-code.html)
- [Introduction to Sandbox Evasion and AMSI Bypasses - Jake Krasnov, Anthony Rose, Vincent Rose](https://www.youtube.com/watch?v=F_BvtXzH4a4)
- [Evading Detection: A Beginner's Guide to Obfuscation](https://www.youtube.com/watch?v=lP2KF7_Kwxk)
- [Exploring PowerShell AMSI and Logging Evasion](https://www.mdsec.co.uk/2018/06/exploring-powershell-amsi-and-logging-evasion/)
- [AMSITrigger v3](https://github.com/RythmStick/AMSITrigger)
- [Bypass AMSI by manual modification](https://s3cur3th1ssh1t.github.io/Bypass_AMSI_by_manual_modification/)

## Resources

- This documentation has been made from my notes of this room on TryHackme:  
{% embed url="https://tryhackme.com/room/avevasionshellcode" %} Tryhackme - AV Evasion: Shellcode {% endembed %}  

- Other convenient resources on the subject includes  
{% embed url="https://captmeelo.com/exploitdev/2018/07/16/backdoor101-part1.html" %} [Backdoor 101] Backdooring PE File by Adding New Section Header - Capt. Meelo {% endembed %}  
{% embed url="https://www.ired.team/offensive-security/code-injection-process-injection/backdooring-portable-executables-pe-with-shellcode" %} Backdooring PE Files with Shellcode - ired.team {% endembed %}  
{% embed url="https://github.com/tkmru/awesome-edr-bypass" %} Awesome EDR Bypass by tkmru {% endembed %}  
{% embed url="https://tryhackme.com/room/hololive" %} TryHackMe - Holo {% endembed %}  