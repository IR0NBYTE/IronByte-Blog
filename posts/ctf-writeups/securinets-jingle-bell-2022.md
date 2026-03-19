---
title: Jingle Bell CTF - 2022 
date: 2022-12-23 17:19:46
---
<center>

Jingle Bell Reverse Engineering Writeups

</center>

![](https://media.tenor.com/2uoTk06PaHYAAAAC/santa-merry.gif)

Jingle Bell CTF purpose was to bring people in the world of cyber security and to let them enjoy the process of problem solving. There are 5 basic tasks + 1 medium task. Enjoy reading !

# I - Simple Check 

![](https://i.imgur.com/ua182Sd.png)

First, you need to download to [Binary.](https://github.com/IR0NBYTE/binaries/blob/main/check)

Let's run it !

**Result :** 
```Console
ironbyte@Med-Ali-Ouachani:~$ ./check 
    ##############################
    ######## SIMPLE CHECK ########
    ##############################

 flag : Ironbyte

 Nope, keep trying !
```

As you can see it's a Crackme Chall, the discription says that it's the easiest crackme ever. Let's just use strings and save the effort for the next ones. 

**Result :**
```Console 
ironbyte@Med-Ali-Ouachani:~$ strings check | grep -a "Securinets{"
Securinets{51mp13_ch3ck_n3x7}
```

**Flag = Securinets{51mp13_ch3ck_n3x7}**

# II - Simple Encryption 



Download the [Binary.](https://github.com/IR0NBYTE/binaries/blob/main/encrypt)

We run a file check :

**Result :**
```Console
ironbyte@Med-Ali-Ouachani:~$ file encrypt
encrypt: ELF 64-bit LSB pie executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, BuildID[sha1]=1fcad62b71a35d88e93f6bdb2ff3d73649d72698, for GNU/Linux 3.2.0, not stripped
```
As you can see it's a dynamically linked ELF not stripped (it means that the functions names and symboles remain the same after compiliation process), maybe if we fire *Ghidra* and do some static analysis on the binary we can reverse it!

![](https://i.imgur.com/m0OepG3.png)

As you can see those are the function that are in the binary, we will navigate to the main and decompile it!

**Result :**
```C
undefined8 main(void) {
  int iVar1;
  long in_FS_OFFSET;
  char local_38 [40];
  long local_10;
  
  local_10 = *(long *)(in_FS_OFFSET + 0x28);
  welcome();
  fgets(local_38,0x20,stdin);
  iVar1 = checker(local_38);
  if (iVar1 == 0) {
    puts("\n  Nope, Keep Trying ! ");
  }
  else {
    puts("\n  Good Job You can Validate with that flag ! ");
  }
  if (local_10 != *(long *)(in_FS_OFFSET + 0x28)) {
                    /* WARNING: Subroutine does not return */
    __stack_chk_fail();
  }
  return 0;
}
```

I will try to rename some variables so that you can understand it better, the first step toward understanding the code is is to change variable names, and redefine structures.

```C
int main(void) {
  int check;
  long in_FS_OFFSET;
  char input [40];
  long stack_Canary;
  
  stack_Canary = *(long *)(in_FS_OFFSET + 0x28);
  welcome();
  fgets(input,0x20,stdin);
  check = checker(input);
  if (check == 0) {
    puts("\n  Nope, Keep Trying ! ");
  }
  else {
    puts("\n  Good Job You can Validate with that flag ! ");
  }
  if (stack_Canary != *(long *)(in_FS_OFFSET + 0x28)) {
                    /* WARNING: Subroutine does not return */
    __stack_chk_fail();
  }
  return 0;
}
```

That's better right? so what he is doing he reading 0x20 bytes from the stdin(Standard Input File) and writes the result in the input variable. After that, he checking the input using the **Check** function. We should analyse that function too (I will try to rename variables for you :) ).

**Result :**
```C
int checker(char* input) {
  uint counter;
  
  counter = 0;
  while( true ) {
    if (0x1f < counter) {
      return 1;
    }
    if (((*(byte *)(input + (int)counter) ^ *(byte *)((long)&key + (ulong)(counter & 3))) !=
         enc_flag[(int)counter]) && (*(char *)(input + (int)counter) != '\0')) break;
    counter = counter + 1;
  }
  return 0;
}
```

As you can see, he is iterating throught the input and xoring each value of the input at the position of the counter (input[counter]) with a value from the the string *key* at the position counter & 3. 

just a little detail :
```Console
# Ways of doing 5 MOD 4 = 1
# 1st Way : 5 & 3 = 1
# 2nd way : 5 % 4 = 1
```
what that means is that we can apply the modulos operation by applying an *And* with the (n - 1) where n refers to the divisor. Next, he is comparing the result of XOR operation with a set of bytes located in enc_flag. I suppose that's the encrypted flag. So that's a simple encryption where he encrypted the flag using a key. Next thing we got to do is find the key and enc_flag in the memory by just clicking on them in Ghidra:

**Result :**
```Console 
key = \x23\x60\xdc\xff
enc_flag = \x70\x05\xbf\xde\x51\x09\xb2\xce\x57\x13\xa7\x9e\x12\x0d\xac\x9a\x10\x3f\xe8\x9e\x7c\x54\xed\xdc\x17\x19\xe9\xc5\x10\x18\xeb\xd6
```

Maybe it's time to write a solver !

```python
#! /bin/python3
# Follow me on Ironbyte.me

# ##################
# Author => IronByte
# ##################

enc_flag = b'\x70\x05\xbf\xde\x51\x09\xb2\xce\x57\x13\xa7\x9e\x12\x0d\xac\x9a\x10\x3f\xe8\x9e\x7c\x54\xed\xdc\x17\x19\xe9\xc5\x10\x18\xeb\xd6'
key = b'\x23\x60\xdc\xff'

flag = ""
for i in range(len(enc_flag)):
    print(chr(enc_flag[i] ^ key[i % len(key)]), end="")
```

**Flag = Securinets{51mp13_45_41w4y5n3x7}**

# III - Simple Keygen

![](https://i.imgur.com/vBoQ3VR.png)

Download the [Binary.](https://github.com/IR0NBYTE/binaries/blob/main/keygen)

Let's run the binary!

**Result :**
```Console 
ironbyte@Med-Ali-Ouachani:~$ ./keygen 
    ##############################
    ###### SIMPLE KEYGEN #########
    ##############################

      Username : mike

      Password : Htprg}{k8<<@

 Nope, Keep trying !
```
As you can see, it's an interface of login. In the description he said find the username with that password, maybe we should do a file check ! 

**Result :** 
```Console
ironbyte@Med-Ali-Ouachani:~$ file keygen
keygen: ELF 64-bit LSB pie executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, BuildID[sha1]=3edd673c581906a79658060deb573a5ea19d608b, for GNU/Linux 3.2.0, not stripped 
```

As always an ELF that is not stripped cool, let's fire the Ghidra and decompile the hell out of it, gain some time and find the main and navigate to it. 

**Result : ** 

![](https://i.imgur.com/vH3g5Z9.png)

```C main 
int main(void) {
  int check;
  char *gen_pass;
  long in_FS_OFFSET;
  char username [32];
  char pass [24];
  long local_10;
  
  local_10 = *(long *)(in_FS_OFFSET + 0x28);
  welcome();
  printf("\n      Username : ");
  fgets(username,0x14,stdin);
  fflush(stdin);
  printf("\n      Password : ");
  fgets(pass,0x14,stdin);
  gen_pass = (char *)gen(username);
  check = strncmp(gen_pass,pass,0xc);
  if (check == 0) {
    puts("\n Good Job you cracked it ! ");
  }
  else {
    puts("\n Nope, Keep trying ! ");
  }
  if (local_10 != *(long *)(in_FS_OFFSET + 0x28)) {
                    /* WARNING: Subroutine does not return */
    __stack_chk_fail();
  }
  return 0;
}
```

As you can see that Keygen is generating some passwords for each username that is entered, Let's navigate to the gen function and analyse it a little!

**Result**
```C gen
void * gen(char *username) {
  void *genstring;
  size_t sVar1;
  int counter;
  
  genstring = malloc(0x14);
  counter = 0;
  while( true ) {
    sVar1 = strlen(username);
    if (sVar1 <= (ulong)(long)counter) break;
    *(byte *)((long)genstring + (long)counter) = (username[counter] ^ 1U) + (char)counter;
    counter = counter + 1;
  }
  return genstring;
}
```

As you can see, he is iterating throught the username and he building the password basically by xoring the username at the position of the counter (username[counter]) and adding the counter to the result. That's a basic Keygen, let's find the username for the password = **Htprg}{k8<<@** and reverse it the way up. 

**Solver :** 
```python
#! /bin/python3
# Follow me on Ironbyte.me

# ##################
# Author => IronByte
# ##################
 
def solve(password):
    ascii = [ord(elt) for elt in password]
    flag = []
    for i in range(len(ascii)): 
        flag.append((ascii[i] - i) ^ 0x01)
    print("Username = ", "".join([chr(elt) for elt in flag])) 
    
solve("Htprg}{k8<<@")
```

**Flag = Securinets{Ironbyte1234}**

# VI - Simple javaEnc

![](https://i.imgur.com/BJWWP8g.png)

As always Download the [Class.](https://github.com/IR0NBYTE/binaries/blob/main/javaEnc.class)

First step is to decompile the class, since we can use an online decompiler let's just use [javadecompiler.com](http://www.javadecompilers.com/), we don't have to use a tool i'm too lazy to open the terminal.

**Result :**
```java main
import java.util.Scanner;

// 
// Decompiled by Procyon v0.5.36
// 

public class javaEnc
{
    public static void welcome() {
        System.out.println("#############################");
        System.out.println("####### JAVA CRACKME ########");
        System.out.println("#############################");
    }
    
    public static String encrypt(final String s, final int n, final int n2) {
        final String s2 = new String("");
        String s3 = "";
        for (int i = 0; i < s.length(); ++i) {
s3 = invokedynamic(makeConcatWithConstants:(Ljava/lang/String;C)Ljava/lang/String;, s3, (char)(s.charAt(i) ^ n ^ n2));
        }
        return s3;
    }
    
    public static void main(final String[] array) {
        welcome();
        try {
            final Scanner scanner = new Scanner(System.in);
            try {
                final String s = new String("");
                System.out.print("Flag : ");
                final String nextLine = scanner.nextLine();
                int nextInt;
                do {
                    System.out.print("Give the 1st byte that can decrypt the flag : ");
                    nextInt = scanner.nextInt();
                } while (nextInt < 0 || nextInt > 256);
                int nextInt2;
                do {
                    System.out.print("Give the 2nd byte that can decrypt the flag : ");
                    nextInt2 = scanner.nextInt();
                } while (nextInt2 < 0 || nextInt2 > 256);
                if (encrypt(nextLine, nextInt, nextInt2).equals("Ucestohcru}l2p2Y>4s15Y`64e7h?Y73Y36Ye667'{")) {
                    System.out.println("\n===> Good Job You cracked the hell out of it ! ");
                }
                else {
                    System.out.println("\n===> Keep trying !");
                }
                scanner.close();
            }
            catch (Throwable t) {
                try {
                    scanner.close();
                }
                catch (Throwable exception) {
                    t.addSuppressed(exception);
                }
                throw t;
            }
        }
        catch (Exception ex) {
            System.out.println("Input won't instance ! ");
        }
    }
}
```

A clear Java code finally, let's start analysing the code. He is asking for an input, then he is reading 2 bytes from the user. After that, he is encrypting the input using those 2 bytes by just xoring each caractere of the input with those 2 bytes and then generating the result and then comparing it with the string "Ucestohcru}l2p2Y>4s15Y`64e7h?Y73Y36Ye667'{". The problem is that we don't have the values of those 2 bytes. Don't forget that a byte ranges from 0 to 255, maybe a BruteForce Attack? we try all the 256*256 combination and grab the flag dierctly of interesting right?

**Solver :**

```python
#! /bin/python3
# Follow me on Ironbyte.me

# ##################
# Author => IronByte
# ##################

inp = "Ucestohcru}l2p2Y>4s15Y`64e7h?Y73Y36Ye667'{"
ascii = [ord(elt) for elt in inp]

def breaker():
    for guess1 in range(0xff):
        for guess2 in range(0xff):
            flagGuess = ""
            for elt in ascii:
                flagGuess = flagGuess + chr(elt ^ guess1 ^ guess2)
            print(flagGuess)
breaker()
```
**A better version**
```py
inp = b"Ucestohcru}l2p2Y>4s15Y`64e7h?Y73Y36Ye667'{"
secret=inp[0]^ord("S")
for i in inp:
    print(chr(i^secret),end="")
```
**Flag = Securinets{j4v4_82u73_f02c1n9_15_50_c001!}**

# V - Simple ASM

![](https://i.imgur.com/TJT78KR.png)

Download the asm [file.](https://github.com/IR0NBYTE/binaries/blob/main/Simple%20ASM.asm)

let's visualize the code:

```asm
SECTION .text

global main

main:
    # input will be a hex value for example (0x12345678)
    mov eax, 0x03
    xor ebx, ebx
    mov edx, 0x4
    lea ecx, DWORD PTR [ebp - 0x4]
    syscall
    # Our input is at the offset ebp - 0x4.
    # Enough Help, Enjoy the algorithm ! 

    mov    eax, DWORD PTR [ebp - 0x4]
    bswap  eax
    mov    edx,eax
    and    eax,0x0f0f0f0f 
    and    edx,0xf0f0f0f0
    shr    edx,0x4
    shl    eax,0x4
    or     eax,edx
    mov    edx,eax
    and    eax,0x33333333
    and    edx,0xcccccccc
    shr    edx,0x2
    shl    eax,0x2
    or     eax,edx
    mov    edx,eax
    and    eax,0x55555555
    and    edx,0xaaaaaaaa
    add    eax,eax
    shr    edx,1
    or     eax,edx
    
    cmp eax, 0xc848b3d5
    je Win 
    jmp Exit

Win: 
    # A function that tells you "Congratulation you can validate with that value !"
    mov eax, 0x04
    xor ebx, ebx
    mov edx, 0x4
    lea ecx, DWORD PTR [ebp - 0x4]
    syscall

Exit: 
    mov eax, 0x01 
    mov ebx, 0x00
    syscall
```

The first lines are used for someting called [linux syscalls](https://www.geeksforgeeks.org/linux-system-call-in-detail/#:~:text=A%20system%20call%20is%20a,systems%20execute%20different%20system%20calls.), their main purpose was to invoke input and output operations to read your input and to write a message for the user. You can have an idea about the values passed to the registers by checking out the [Linux Syscall Table](https://chromium.googlesource.com/chromiumos/docs/+/master/constants/syscalls.md#x86-32_bit). After that, there is a whole algorithm that is working on the input that we pass that generates an output loaded in the *EAX* register. The best way to understand such an algorithm is just by executing it. 

```asm
mov eax,DWORD PTR [ebp - 0x04]  eax =       12 34 56 78
bswap eax                       eax =       78 56 34 12
mov edx,eax                     edx =       78 56 34 12
and eax,0x0f0f0f0f              eax =       08 06 04 02
and edx,0xf0f0f0f0              edx =       70 50 30 10
shr edx,0x4                     edx =       07 05 03 01
shl eax,0x4                     eax =       80 60 40 20
or eax,edx                      eax =       87 65 43 21
mov edx,eax                     edx =       87 65 43 21
and eax,0x33333333              eax =       03 21 03 21
and edx,0xcccccccc              edx =       84 44 40 00
shr edx,0x2                     edx =       21 11 10 00
shl eax,0x2                     eax =       0c 84 0c 84
or eax,edx                      eax =       2d 95 1c 84
mov edx,eax                     edx =       2d 95 1c 84
and eax,0x55555555              eax =       05 15 14 04
and edx,0xaaaaaaaa              edx =       28 80 08 80
add eax,eax                     eax =       0a 2a 28 08
shr edx,1                       edx =       14 40 04 40
or eax,edx                      eax =       1e 6a 2c 48
ret
```

Input in the base 2:

```
0x12345678 = 0001 0010 0011 0100 0101 0110 0111 1000
```

Output in base 2: 

```
0x1e6a2c48 = 0001 1110 0110 1010 0010 1100 0100 1000
```

As you can see the algorithm is reversing the bits of the input, so to find the input for **0xc848b3d5** all we have to do is apply the same algorithm by just reversing it's bits. One thing to mention is that algorithm is pretty famous used in a lot of algorithms such as FFT(Fast fourier Transformation) and it's called the *Generalized Bit Reversal*.

**Solver :** 
```py
#! /bin/python3
# Follow me on ironbyte.me

# ##################
# Author => IronByte
# ##################

# O(N) Solution.
def reverseBits(n):
    rev = 0
    while (n > 0):
        rev = rev << 1
        if (n & 1 == 1):
            rev = rev ^ 1
        n = n >> 1
    return hex(rev)

# O(1) Solution.
def reverseBits_Optimized(x):
    x = (x & 0x55555555) << 1 | (x & 0xAAAAAAAA) >> 1
    x = (x & 0x33333333) << 2 | (x & 0xCCCCCCCC) >> 2
    x = (x & 0x0F0F0F0F) << 4 | (x & 0xF0F0F0F0) >> 4
    x = (x & 0x00FF00FF) << 8 | (x & 0xFF00FF00) >> 8
    x = (x & 0x0000FFFF) << 16 | (x & 0xFFFF0000) >> 16
    return hex(x)


value = 0xc848b3d5
print("Value with the unOpt : ", reverseBits(value));
print("Value with the Opt : ", reverseBits_Optimized(value))
```

**Flag = Securinets{0xabcd1213}**



# VI - Sonara

![](https://i.imgur.com/Qoh0st5.png) 

First, you need to download the [Binary.](https://github.com/IR0NBYTE/binaries/blob/main/Sonara)


We come now to most intersting task in the CTF, Without any hesitation we run it :

**Result :**

```Console
ironbyte@Med-Ali-Ouachani:~/Securinets-Chrismas-CTF-2022/Reversing/Sonara$ chmod +x Sonara && ./Sonara 
################################### 
############# SONARA ############## 
################################### 
Flag : follow me on ironbyte.me  
Sonara SHUTDOWN NOW !
```

it looks like it's a Crackme chall, maybe a file check on the bin ? 

**Result :**
```Console
ironbyte@Med-Ali-Ouachani:~/Securinets-Chrismas-CTF-2022/Reversing/Sonara$ file Sonara
Sonara: ELF 64-bit LSB executable, x86-64, version 1 (SYSV), statically linked, Go BuildID=CVvDxsixuIZcRmasFGLC/E529yWg17scgwebE8Lry/xCKiku9j0z8fuvR7RNTk/MlzQJ_9EXHc-RV7RoJPK, stripped
```
As you can see it's an ELF 64-bit Little Endian executable that is statically linked, which means that all the libraries that the binary needs are already compiled into it, that's why it's size is bigger than the usual ones, another reason come from the fact that it's a GO (you can know that from the "Go buildID") binary cuz usual C/C++ binaries are much lighter than GO binaries. Another thing to notice, is the stripped keyword which means that the symboles and the functions names are stripped from the binary. Since, it's a stripped binary it's useless to run *strings*  on it !

Let's not waste too much time and start the static-analysis by firing Ghidra & IDA :

![](https://i.imgur.com/YyneEKf.png)

It looks like that this binary is a compiled **GO** Binary. As you can see the first wall that we are facing is the fact that the function names are stripped ! How are we gonna find our main function ? How are we gonna solve this problem? the answer is simply using plugins and scripts. If you are using IDA you should add those [plugins](https://github.com/strazzere/golang_loader_assist) to your IDA, otherwise if you are using Ghidra you should add those [scripts](https://github.com/getCUJO/ThreatIntel) to your little Dragon.

Let's navigate into the script-manager in Ghidra :

**Result :** 
![](https://i.imgur.com/d7jKJXF.png)

Let's run the script and recover our functions : 

**Result :** 
![](https://i.imgur.com/0KlYHag.png)

As you can see we have recoverd the functions names, I will now complete the rest of the static analysis using IDA, I will navigate into the main located at the Address **0x0048c980** and disassemble the whole main !

**Result :** 
![](https://i.imgur.com/83MBLKL.png)


```assembly main
sub     rsp, 1B8h
mov     [rsp+1B8h+var_8], rbp
lea     rbp, [rsp+1B8h+var_8]
call    Weclome
nop
mov     rbx, cs:qword_53A1F0
lea     rax, off_4C44B8
lea     rcx, unk_4A41B0
mov     edi, 7
xor     esi, esi
xor     r8d, r8d
mov     r9, r8
call    sub_481360
lea     rax, byte_494360
nop     dword ptr [rax]
call    sub_40BE80
mov     [rsp+1B8h+var_30], rax
mov     qword ptr [rax], 0
movups  [rsp+1B8h+var_18], xmm15
lea     rcx, unk_4929A0
mov     qword ptr [rsp+1B8h+var_18], rcx
mov     qword ptr [rsp+1B8h+var_18+8], rax
mov     rbx, cs:qword_53A1E8
lea     rcx, [rsp+1B8h+var_18]
mov     edi, 1
mov     rsi, rdi
lea     rax, off_4C4498
call    sub_487CA0
mov     rax, [rsp+1B8h+var_30]
mov     rbx, [rax]
mov     rcx, [rax+8]
lea     rax, [rsp+1B8h+var_C0]
call    sub_448A20
mov     [rsp+1B8h+var_38], rax
mov     [rsp+1B8h+var_170], rbx
movups  [rsp+1B8h+var_166], xmm15
movups  [rsp+1B8h+var_166+6], xmm15
movups  [rsp+1B8h+var_150], xmm15
mov     rcx, 0CADCD2E4EAC6CAA6h
mov     qword ptr [rsp+1B8h+var_166], rcx
mov     rcx, 0BEF266D06EF6E6E8h
mov     qword ptr [rsp+1B8h+var_166+8], rcx
mov     rcx, 62BE6072BEF2686Ah
mov     [rsp+62h], rcx
mov     rcx, 0EACCBE66D06EBE6Ah
mov     qword ptr [rsp+1B8h+var_150+2], rcx
mov     dword ptr [rsp+1B8h+var_150+0Ah], 6664EA6Eh
mov     word ptr [rsp+1B8h+var_150+0Eh], 0FA42h
xor     ecx, ecx
xor     edx, edx
xor     esi, esi
jmp     short loc_48CAE1
```

from line 1 to 4 is dedicated to the Welcome function after some investigations, you can investigate it too ! 

**Result :**
![](https://i.imgur.com/8qYlN8Q.png)

we can try to check the **off_4c** in the function and try to see what the binary is trying to print! 

**Result :**
![](https://i.imgur.com/qkEnplS.png)

As you can see it's printing the banner, don't forget that in **GO** we use the *fmt.Println* to print text to the output. Let's keep analysing the rest of the program !

From line 5 to 38 it looks like he is preparing for the input and is printing the string **"Flag : "** using the **sub_481360** (refers to *fmt* function) cuz he already passed the string **flag : ** as an argument before calling it. A cool article that can help you understand better what i'm talking about is this [Go Calling Convention](https://dr-knz.net/go-calling-convention-x86-64.html).

One thing to keep track is that he is saving our input **offset = rsp+1B8h+var_38** in the stack and the length of the input at the offset **offset = rsp+1B8h+var_170**.

If we continue we see that he is loading some bytes into the *RCX* register and then charging those bytes into stack (line 39 - 48). The first 8 bytes = "CA DC D2 E4 EA C6 CA A6" it looks like that he is chaining those bytes and throwing them into the stack. Dont't forget that this is an LSB binary which means that the order is reversed which means that for example the first bytes = "CA DC D2 E4 EA C6 CA A6" should be equal to "A6 CA C6 EA E4 D2 DC CA", same goes for the rest of the bytes. So if we chain all of them we will have bytes = "A6 CA C6 EA E4 D2 DC CA E8 E6 F6 6E D 66 F2 BE 6A 68 F2 BE 72 6 BE 62 6A BE 6E D 66 BE CC EA 6E EA 64 66 42 FA". That looks like an array of bytes that he might be using !

From line 49 - 51, he moved 0 to the *ECX*, *ESI*, *EDX* (the 32bit parts of those registers).
I suppose we are starting a loop, since the *RCX* is known for being the counter of loops!

![](https://i.imgur.com/9eZH23R.png)

```assembly
mov     [rsp+1B8h+var_178], rdx
mov     [rsp+1B8h+var_180], rcx
mov     [rsp+1B8h+var_40], rsi
lea     rdi, [rax+rcx*4]
movsxd  rdi, dword ptr [rdi]
mov     r8, rcx
shr     rcx, 63
add     rcx, r8
sar     rcx, 1
shl     rcx, 1
sub     r8, rcx
cmp     r8, 1
jnz     short loc_48CB4A
```
I suppose this is a clear loop as you can see, first he is comparing the *RCX* with the value of *RBX*. Since the *RBX* contains the length of the input, he is looping throught the input and as you can see in the instruction in line 4 he is loading the input at the position pointed to by **RCX*4*** (input[RCX]). Line 5 - 11, is what we call a compiler optimization that actaully do the *modulus* operation of the input[RCX] by RCX. It checks whether the value of input[RCX] % RCX = 1. If it's equal to 1 it will execute the **Loc_48CB4A** block as you can see.

```Assembly
loc_48CB4A:
shl     rdi, 1
movsxd  rbx, edi
xor     eax, eax
call    sub_448BE0
mov     rcx, [rsp+1B8h+var_178]
mov     rdi, rax
mov     rsi, rbx
xor     eax, eax
mov     rbx, [rsp+1B8h+var_40]
call    sub_448400
jmp     loc_48CAC5
```

What this block is doing, it takes the input that is saved in *RDI* and it's shifting it to the left by 1. I suppose the the first **sub_448BE0** function is used for casting the value back to char or string and the other **sub_448400** is used for affecting that value to the generated string at the offset = **rsp+1B8h+var_140**. 

Let's see the else block : 

```Assembly
shl     rdi, 1
movsxd  rbx, edi
xor     eax, eax
call    sub_448BE0
mov     rcx, [rsp+1B8h+var_178]
mov     rdi, rax
mov     rsi, rbx
xor     eax, eax
mov     rbx, [rsp+1B8h+var_40]
xchg    ax, ax
call    sub_448400
jmp     loc_48CAC5
```

As you can see it's doing the same thing as the other block except that he is not xchg the value of *AX* since it's not full a left shift operation, it's actually a multiplication operation by 2, it means multplying the input at the position **RCX** by 2.

The next block is used for incrementing the value of **RCX** by 1 by loading the saved value of it from the offset **rsp+1B8h+var_180**. 

I suppose he was encrypting the input by that simple algorithm, let's analyse the else block of the **loc_48CAE1**. 

![](https://i.imgur.com/OYUMRvb.png)

Let's with the first block :

```Assembly
loc_48CB76:
lea     rax, [rsp+1B8h+var_140]
mov     rbx, rsi
mov     rcx, rdx
call    sub_448A20
xor     ecx, ecx
jmp     short loc_48CB8D
```

It's just loading the length of the generated string into **RBX** and moving 0 to **RCX**. Again, it's another loop i suppose he will be looping throught that generated string.

```Assembly
loc_48CB8D:
cmp     rbx, rcx
jle     short loc_48CC01
```

This block is for checking if we are at the end of the string or not, if the value of **RBX** is lower or equal to **RCX** than we go to the **loc_48CC01** which contain our *Congratualtion Message*.

```Assembly
loc_48CC01:
call    win
mov     rbp, [rsp+1B8h+var_8]
add     rsp, 1B8h
retn
```

The else block : 

```Assembly
mov     edx, [rax+rcx*4]
cmp     rcx, 38
jnb     short loc_48CC16
```
The else block, he is loading the byte that is located at the **RAX** which contains address of the generated string at the offset **RCX*4**. After he is comparing the value of **RCX** with 38 than jumps to this block :

```Assembly
movzx   r8d, byte ptr [rsp+rcx+1B8h+var_166]
cmp     edx, r8d
jz      short loc_48CB8A
```

This block is loading one byte pointed by **RCX** of the value located at the offset **rsp+rcx+1B8h+var_166** into the *R8D* register, that offset actually is the same offset of the bytes that we have found at the beggining of our analysis. After that, it's comparing that value with the generated value. The next blocks one for incrementing the **RCX** (Counter by 1), and the other one i suppose is for the fail function.

I suppose the encryption basically simple but reversing GO binaries is not that simple, GO been known for being a language that many Mallwares been written with, it's a powerful language worth your time to learn and to master.

We can write a python script that can reverse the code now : 

```Python
enc_flag = [166, 202, 198, 234, 228, 210, 220, 202, 232, 230, 246, 110, 208, 102, 242, 190, 106, 104, 242, 190, 114, 96, 190, 98, 106, 190, 110, 208, 102, 190, 204, 234, 110, 234, 100, 102, 66, 250]

flag = []
for rcx in range(len(enc_flag)): 
    flag.append(enc_flag[rcx] >> 1)

print("".join(chr(elt) for elt in flag))
```
**Flag = Securinets{7h3y_54y_90_15_7h3_fu7u23!}**

I hope you enjoyed playing the Securinets-Christmas CTF 2022, never stop learning and feel free to reach me out either on facebook or in real life if you have any questions.

