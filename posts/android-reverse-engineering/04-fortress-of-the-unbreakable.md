---
title: Fortress Of The Unbreakable
date: 2023-04-16 12:08:22
---

<center>
    Chapter 4 : Fortress Of The Unbreakable
</center>

![](https://media.tenor.com/0bXnlQfC5PUAAAAC/goblin-slayer-power.gif)

If you have skipped previous sections, it is assumed that you have a basic understanding of reverse engineering and knowledge of the Android architecture. However, if you need to brush up on the fundamentals, please take your time to review the previous chapters before proceeding further.


### I - Motivation
Hey there! If you're interested in advanced Android topics, I wanted to share with you some exciting stuff about the native code. If you're already familiar with the basics of reverse engineering and have some knowledge about Android architecture, then you're in the right place. If not, don't worry - take your time to review the previous chapters, and then come back here for some truly fascinating insights.

### II - Native Code
Do you remember our previous discussions on the topic of native code and dynamic code loading? I believe it's time to dive deeper into this advanced topic, and to that end, I've decided to dedicate a whole chapter to it. By the way, do you recall the picture I showed you on this topic?

![](https://i.imgur.com/7C8J5kZ.png)

Native code typically refers to code that can be executed directly by the machine, without the need for an intermediate layer like ART. This code is usually written in C++ or C. Wait a sec, a question why do we use it? Well, it comes out that in Android app development, there are certain components that require high performance, such as image and video processing, audio processing, and gaming. For these components, the use of Java or Dalvik (the virtual machine that executes Android apps) might not be efficient enough to provide a smooth user experience. To overcome this issue, Android allows developers to write these performance-critical components in C or C++, which can be compiled into native code and executed directly by the machine. This native code is usually faster than Java or Dalvik components because it is closer to the machine's hardware and can take advantage of the low-level optimizations provided by the compilers. However, Google discourages the use of native code in Android app development because it can introduce security risks and make the app more difficult to maintain and update. Nevertheless, some mallware authors might not care about these concerns and may use native code to create malicious apps.
One important aspect to consider is that working with native code is generally more challenging than working with Dalvik bytecode. This is due to the fact that Dalvik bytecode verification makes disassembling and analyzing relatively straightforward. The bytecode follows specific properties that enable easier disassembling and analysis. On the other hand, disassembling machine code remains a significant challenge, since it is not possible to obtain the exact original code. When native code is used in Android, it becomes difficult to disassemble and decompile the code.This makes it a great trick for attackers, as it can make it harder for security researchers to analyze the code and find vulnerabilities. In addition, the use of native code provides a great venue for code obfuscation, as developers can use various techniques to make it even harder to analyze the code. Furthermore, analyzing just the Java code in an Android app is not enough when native code is used. It is important to analyze the native code as well, as it may contain vulnerabilities that cannot be found in the Java code. Moreover, the analysis of Java code can be misleading when native code is involved, as the Java code may call native functions that perform actions that are not visible in the Java code.

In Android, both Java/Dalvik code and native code run within the same security sandbox. This means that even though native code has the ability to interact with the system at a lower level than Java/Dalvik code, it still operates within the same security restrictions as the higher level code. However, native code has the ability to interfere with Dalvik code and memory. For example, native code can modify memory data structures, which could potentially cause the Java code to behave unexpectedly or even crash. Additionally, an attacker could modify the native code to change the behavior of the app at runtime. Add to that, since everything runs as the same user, any malicious behavior within the native code can potentially affect the entire system, including other apps and the user's data.
But wait how does the java code speaks with the native code? To enable communication between Java and native code, the Java Native Interface **JNI** is used, and its's not the only way.

![](https://2951263804-files.gitbook.io/~/files/v0/b/gitbook-legacy-files/o/assets%2F-LEyKyxZqxfQ560k-THK%2F-MES5YbaLPHe5sYbJaFR%2F-MESB0fF80Cws5cYVge5%2FJNI.svg?alt=media&token=f19cf566-de85-43d8-b390-39666bc84a3a)


For example: 

The native code would look like that: 

```c++
#include <jni.h>

JNIEXPORT jint JNICALL Java_MyPackage_MyClass_addTwoNumbers(JNIEnv *env, jobject obj, jint num1, jint num2) {
    return num1 + num2;
}
```
Than you need to save the file in the format libName_of_the_lib.so.

One way to include native code in your Java code is as follows:

```java
package Mypackage;
public class MyClass {
    public native int addTwoNumbers(int num1, int num2);
    static {
        System.loadLibrary("Name_of_the_lib");
    }
}
```

We can test the method: 

```java
MyClass myObj = new MyClass();
int result = myObj.addTwoNumbers(2, 3);
System.out.println("Result is : " + result);
```

Okay wait a sec, what about the "JNIEXPORT", "JINCALL", and the "JNIenv* env" pointer what's the purpose of this parms exactly? well let's start with the "JNIEXPORT" macro, this macro is used basically to tell the compiler hey this function should be exported as a symbole in the shared library (for linux) or as a DLL (for windows) so that it can be accessed by the JVM when the library is loaded. One thing to note here, the JVM is responsible for loading the shared library, while the functions within the library are then accessed through the JNI interface, so it's not the one responsible for executing the code, it's only responsible for loading and accessing the code. Next, JINCALL is a macro that specifies the calling convention for the native method. The calling convention is the set of rules that determine how the arguments are passed and how the return value is returned. In general, the calling convention should be set to match the platform's default calling convention. Next, JNIenv* env pointer, well with a little of bit of knowledge in C, you can spot that this is a pointer on a structure. The JNIEnv structure provides various functions and data structures that are used by native methods to interact with the JVM and the Java objects and classes that are used by the application. For example, the JNIEnv structure provides functions for creating, manipulating, and releasing Java objects, as well as for calling Java methods and getting and setting field values. Some of the most commonly used functions provided by the JNIEnv structure include:

* **GetObjectClass**: retrieves the jclass object for a given Java object.
* **GetMethodID**: retrieves the ID of a Java method based on its name and signature.
* **CallObjectMethod**: calls a Java method that returns an object.
* **CallBooleanMethod**: calls a Java method that returns a boolean value
* **CallIntMethod**: calls a Java method that returns an integer value.
* **CallVoidMethod**: calls a Java method that does not return a value.
* **NewObject**: creates a new instance of a Java class.

Here's the full structure [implementation](https://docs.google.com/spreadsheets/d/1yqjFaY7mqyVIDs5jNjGLT-G8pUaRATzHWGFUgpdJRq8/edit#gid=0).

Here's a better example so that you can understand more what i'm talking about. 

```c++
#include <jni.h>
#include <string.h>
#include <stdio.h>

JNIEXPORT jstring JNICALL Java_my_package_MainActivity_getJniString(JNIEnv* env, jobject obj) {

    jstring jstr = env->NewStringUTF(env, "Created in JNI");
    jclass clazz = env->FindClass(env, "my/package/MainActivity");
    jmethodID messageMe = env->GetMethodID(env, clazz, "messageMe", "(Ljava/lang/String;)Ljava/lang/String;");
    // (Ljava/lang/String;)Ljava/lang/String; : that's Called the signature of the method. 
    jobject result = env->CallObjectMethod(env, obj, messageMe, jstr);
    const char* str = env->GetStringUTFChars(env,(jstring) result, NULL);
    printf("%s\n", str);

    return env->NewStringUTF(env, str);
}
```

You can read more about JNI [@offical docs](https://developer.android.com/training/articles/perf-jni).


### Time Machine Activated

It's time to go backwards now, so the first step is to download the APK from this [link](https://github.com/IR0NBYTE/CTfs/blob/main/checkflag.apk).

The purpose of this APK is to facilitate the learning process by providing an easy-to-use app that allows you to practice reverse engineering techniques on the native code.

Let's start by installing the apk, using adb: 

```shell
adb install checkflag
```

We can run now the app: 

![](https://i.imgur.com/nha7UZR.png)

As you can it's just a simple app, that is asking for a flag. Let's start some basic static analysis using JADX. 

![](https://i.imgur.com/ZDkrzX5.png)

Alright, if you take a look here, you'll see several native methods being invoked, and one of them is responsible for verifying the flag. It might be worthwhile to examine this function more closely and gain a better understanding of its behavior. 

Let's begin by unpacking the APK using [apktools](https://ibotpeaches.github.io/Apktool/). After that, we can navigate to the lib directory to find all the native libraries.

![](https://i.imgur.com/dF7vT7U.png)

Alright, we've located the native library, and it seems like there are various libraries for different architectures. This is because the native code is designed to run on multiple architectures, hence each architecture has its own specific library. It's quite interesting, isn't it? I will chose the ARM version and don't worry in the next chapter, we will dive more into ARM with more details.

Let's run the a file check: 

```shell
ironbyte@ironbyte:$ file libcheckingflag.so
libcheckingflag.so: ELF 64-bit LSB shared object, ARM aarch64, version 1 (SYSV), dynamically linked, BuildID[sha1]=e7eaecc1c0aa624b0c7c18ec92d50dd2e15793a1, stripped 
```

Therefore, we are dealing with an ARM arch64 ELF binary that has been stripped, making it more difficult to reverse engineer because the function symbols have been removed. Additionally, it is dynamically linked, which means that the linker links the necessary libraries during runtime.

Alright, we now need to analyze this library and we can use IDA or Ghidra for this purpose. I personally prefer IDA as it offers a more user-friendly interface to work with.

Let's try to find out where the name of the native function invoked using strings: 

![](https://i.imgur.com/OdX46nt.png)

Next, let's navigat to the function and check it out: 

![](https://i.imgur.com/F40pOro.png)
The code is simply comparing our input with the hardcoded flag. Though it's an easy task, this exercise provides us with valuable insight into the process of analyzing native code. Let's give it a try!

![](https://i.imgur.com/KKBtwyT.png)

It appears that we have successfully solved the problem. Although this is a very basic example of reverse engineering native code, sometimes it is necessary to understand ARM assembly code and have a deeper understanding of it. Some code may also be obfuscated, so it is important to be proficient in using either IDA or Ghidra to reverse engineer it effectively. Next chapter, will tackle ARM with more detailes.

Same ending, don't hesitate to reach out to me if you have any questions! We're all on this journey together. You can find me on Discord at @IronByte#0855 or connect with me on [LinkedIn](https://www.linkedin.com/in/med-ali-ouachani/). And hey, make sure to follow me on Twitter too [@ir0nbyte](https://twitter.com/Ir0nbyte) . See you in the next chapter!














































