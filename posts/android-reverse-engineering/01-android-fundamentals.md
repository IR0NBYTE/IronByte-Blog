---
title: Android Fundamentals
date: 2023-03-30 17:29:59
categories: Reverse Engineering
---
<center>

Chapter 1 : Android Fundamentals

</center>


![](https://steamuserimages-a.akamaihd.net/ugc/771727728509867103/E4E258DE55817B21025FD3D19ED18003D7411331/?imw=512&&ima=fit&impolicy=Letterbox&imcolor=%23000000&letterbox=false)

The aim of these chapters is to dive into the Android framework, and examine the process of reverse engineering and exploiting Android applications. I'm saving the date it's march 2023, I am offically sharing some of the knowledge aquired along this journey.


### I - Motivation
The motivation behind this series of articles was a series of questions that I had been pondering for some time since i was in high school. Before diving into the world of the Android Operating System and its apps, I asked myself why am I interested in the first place, what benefits there was to seek, and how this knowledge could be used to impact the world. Reflecting on my personal experience with smartphones, I realized that despite using them for various activities, I had never considered how the apps actually worked and what was happening behind the scenes. Although I knew that the Android OS was responsible for this, but at that time I lacked the necessary programming knowledge to understand it. However, with fair knowledge of programming and computer science, I became interested in understanding the Android framework, the entire app development process, and even identifying security flaws and reverse-engineering Android apps.

### II - The Android Framework 
The goal of this chapter is to get you familiarized with the Android system. Deep down, I was always thinking that I won't be able to effectively attack and exploit it if I don't understand how it works. It's all about building a solid foundation of theoretical knowledge that I can link to practical application. As the saying goes, "Without understanding how something was constructed, it is impossible to reverse engineer it". Make sure to commit this sentence to your memory and keep it in your mind throughout this journey.

First, let me begin by introducing the concept of a framework. Once we have a good understanding of what a framework is, we can then explore the Android Framework in more detail together.

* **Framework** : A framework is a collection of pre-built tools and libraries that allow you to create a specific project without having to start from scratch every time you want to build something new. This revolutionary concept makes application development faster and more efficient. Let me give you **an example** to understand more what i'm talking about since I love examples, So Imagine that you want to build a big tower out of blocks. You could do it by starting from scratch every time, but that would take a lot of time and effort. Instead, you could use a framework, which is like a set of instructions or rules that tell you how to build the tower. For example, The framework might say things like "start with a strong foundation," "make sure the blocks are all lined up," and "add more blocks to make the tower taller." By following these rules, you can build a tower much faster and more efficiently than if you had to figure it out all on your own.

Now that we have a solid understanding of what a framework is, let's take a closer look at the Android Framework and explore how it works. Most of the Android apps we use operate within the Android framework, meaning that they use pre-built library functions and **APIs** called **Android APIs**. These APIs are often an extension of Java APIs, so many Java APIs are also part of the Android APIs. But there is a question that i asked myself, *what is an API?* to put it simply, an API is like a waiter in a restaurant. Just like a waiter takes your order and delivers it to the kitchen, an API takes your request and delivers it to the service or program that can fulfill it. For example, if you use a weather app, you use their API to request information about the weather, and the API communicates with a weather service to retrieve the current weather conditions for your location and display them to you. Similarly, if you use a map app, it uses APIs to get information about your location and display it on the map. You need to  understand that asking questions is part of the jouney cuz I've been asking a lot of questions lately, and I've realized that it's the key to learning new things. Don't be afraid to ask questions to urself, because they can help you understand things better and deepen your knowledge.

Now that we have introduced the Android Framework, it's time to dive deeper into it's architecture. Here's a picture of the layers:

![](https://i.imgur.com/BrX9D58.png)

I will try to explain each layer alone so that you can understand the framework pefectly, i will go from top to buttom. 

* **System Apps**: This layer is responsible for providing core functionality to the device. These  system apps such as the phone dialer, messaging app, web browser, and other apps are pre-installed on the device and typically cannot be uninstalled by the user. They are designed to provide critical services such as managing the device's battery life, handling system updates, and ensuring security. 

* **Java API Framework**: The Java Framework API layer is like a toolbox for developers who want to create their own apps. It provides them with pre-built libraries and APIs that they can use to make their apps work. It includes *components* like buttons, text views, and layouts for creating the app's user interface. The layer also has classes for storing data, communicating over the internet, and working with things like photos and videos. One thing to mention, is that the android API framework is built upon this layer which means when you invoke an API that is in the android API framework you might invoke a methode or an API from the Java API Framework.
Here's an example of an API that can handle HTTP request to my blog: 
```java
URL url = new URL("http://www.ironbyte.me/");
HttpURLConnection urlConnection = (HttpURLConnection) url.openConnection();
try {
   InputStream in = new BufferedInputStream(urlConnection.getInputStream());
   readStream(in);
} finally {
   urlConnection.disconnect();
}
```

* **Native C/C++ Libraries**: Native C/C++ libraries are a set of pre-built code modules that are written in the C or C++ programming languages. They provide a way for developers to access low-level hardware and system functions on an Android device that cannot be accessed using Java. These libraries are used to build high-performance, low-level system components such as device drivers, audio and video codecs, and signal processing algorithms. *But why did we use native C/C++ libraries?* One of the main advantages of using Native C/C++ libraries is their ability to execute code faster and more efficiently than Java. This is because C/C++ code is compiled to native machine code, which can be executed directly by the device's processor, without the need for a virtual machine like the Java Virtual Machine (JVM). You can check my articles about reverse engineering x86 and x64 to understand more the process of compiling C/C++. Another question that i asked was, *what is a driver?* simple just Imagine you have a toy car that you want to control using a remote control. The remote control sends signals to the car's motor, telling it to move forward, turn left or right, or stop. But in order for the remote control to send those signals, it needs to be connected to the car's motor, right? Well, in the same way, a computer needs to be able to communicate with all the different parts inside it, like the hard drive, the keyboard, the mouse, and so on. But the computer doesn't speak the same language as these parts! That's where drivers come in. A driver is like a translator between the computer and a specific piece of hardware. It helps the computer understand how to communicate with that hardware, so that you can use it. Just like the remote control needs to be connected to the car's motor, the driver needs to be installed on the computer and connected to the hardware. I spent a while explaining what drivers are, but it's important to know that when people create drivers, they often use programming languages such as C/C++.

The three layers mentioned earlier are known as the High-Level Layers because they involve the use of high-level programming languages like C/C++ and Java and the some other user stuff. Now, let's shift our focus to the Low-Level Layers. These layers are used by the Android operating system to communicate directly with the device.

* **Android Runtime**: Android Runtime, i would love to call it also ART, is a component of the Android operating system that is responsible for running Android apps. It is a virtual machine that executes code written in Java or Kotlin programming languages. *Wait a minute what's a virtual machine?* Great question, let me go back to the example of the car toy when i wanted to explain to you the *drivers* concept. Now try to Imagine you have a toy box with lots of different toys in it. Each toy can do different things, like a toy car can roll around and a toy airplane can fly. But you can only play with one toy at a time. Now, imagine you have a magical box that can make copies of your toy box. You can open the magical box and make a copy of your toy box, and now you have two identical toy boxes. You can play with one toy in the original toy box, while your friend plays with a different toy in the copy toy box. So a virtual machine is like that magical box. Instead of toy boxes, it makes a copy of a computer's operating system and programs. This way, multiple people can use the same computer, but each person can have their own copy of the operating system and programs, and they won't affect each other. The toys in my example, are typically programs that can be an *APK*, an *ELF* file, a *PE* file or anything else. For example you can run a linux virtual machine and run linux programs on a windows computer and the opposite is true. Now back to the ART case, ART is a virtual machine that understands only bytecode, it's the one responsible for executing your code after the translation of the java code into bytecode. Whenever you use an app on your phone, ART reads the instructions in the app that are written in Java and translates them into a language that your phone can understand. I have got one thing to mention, before the ART existed there was another virtual machine that was responsible for executing the bytecode called the *Dalvik Virtual Machine* that gets the bytecode and and translate it into Dalvik bytecode and than execute it, don't worry we will dive more into details about the ART and the DVM in the [Reverse Engineering ~ Chapter 3 : Smashing The APK](../Android-REV-3/).

* **Hardware Abstraction (HAL)**: This layer is like a bridge between the high-level layers (which we talked about earlier) and the low-level layers that are closer to the hardware of the device.The HAL layer is responsible for providing a standardized interface between the higher-level and the hardware-specific drivers that interact with the physical components of the device, such as the camera or mic. Essentially, it allows the operating system to communicate with the hardware without needing to know the specific details of each device's hardware components. For example, if an app wants to take a photo on an Android device, it will use the camera API provided by the Android framework. The camera API will then communicate with the HAL layer to access the camera driver and control the camera hardware to take the photo. It's simple, we don't have to complicate things too much.

* **Linux Kernel**: The Linux kernel layer is the part of the Android operating system that interacts directly with the device's hardware. This is actually were the android operating gets his power from. The kernel is like the brain of the operating system, as it controls how the software interacts with the device's hardware. It provides low-level services like memory management, process management, device drivers, and security. For example, when you open an app that uses the camera, the app interacts with the Android camera API, which in turn sends a request to the Linux kernel layer to access the camera hardware. The kernel then controls how the hardware is accessed and manages the flow of data between the app and the camera.

I hope i managed to explain to you the different components of the Android framwork, if you are still curious you can find more details about the Android Arch more [@Android-Arch](https://developer.android.com/guide/platform). Keep in mind that the concepts we are learning now serve as a foundation for the next chapters, this is only the beginning!

Now it's time to explore the fundamental concepts of Android apps. Each app has a unique package name that identifies it, such as "com.facebook.katana" for Facebook. This naming convention is crucial because it distinguishes one app from another. To be installed on a device, every app must have a distinct package name. For example, If you create two apps with the same package name, only the first one installed in the system will remain.
One more thing to mention, in Android app development it's worth noting that there is no "main" function like in C and C++. Instead, users interact with the app through the Graphical User Interface (GUI) which includes various types of widgets like Edit Text and Buttons, and there is no command line interface. Additionally, many APIs in Android are event-driven, it means that they are triggered by listeners.
* Step 1: You register a "listener" X.
* Step 2: X callback is inovked later on.

Simple right?
Okay now back the *components*, it's time to explain them since apps are made up of various components. Anyway, Components are like the building blocks that make up an Android app. Just like how you can use blocks to build a tower or a castle, developers use components to build an app.
There are four main types of components:

* **Activity**: An Activity is a component that represents a single screen with a user interface. It is one of the four main components that make up an Android app. An Activity typically provides a window where the user can interact with the app, such as displaying text, images, and other UI elements, and handling user input. You can have many such that each of them defines a UI, and the interesting thing is that you can define which one is the "main" one that will be executed when you start the app. 
* **Service**: A Service component is a type of component that runs in the background and performs long-running operations or tasks that do not require user interaction. For example, Spotify is using a music player service, to play the music in your phone while you are not in the app.
* **Broadcast Receiver**: A Broadcast Receiver is a type of component that is designed to respond to system-wide events. Its primary purpose is to handle and react to events that occur within the Android system, such as battery charging or the receipt of a text message. These receivers have a well-defined entry point, and the system is capable of delivering events to apps that are not currently running.
* **Content Provider**: A Content Provider is a component that manages access to a shared set of data. It provides a standard interface that allows other applications to query and modify the data. A Content Provider can be thought of as a mediator between an application and a data source, providing a level of abstraction that allows an application to interact with the data without worrying about the underlying details of how it is stored or managed. What's interesting is that content Providers are typically used to share data between multiple applications or to provide access to data that is stored in a central location, such as a database or a file. They are also commonly used to provide access to system data, such as contacts or media files which makes them a great target for exploits.


To make an anologie, to let you understand each of this components more clearly imagine them like that: 
* Activities are like the rooms in a house. They represent a single screen with a user interface, and users can interact with them by tapping buttons or typing in information. One more thing the Activity that most of the apps start executing once the app is loaded called the mainActivity. 
* Services are like the workers in a house. They run in the background without any user interface, and they perform tasks like playing music or downloading files.
* Broadcast receivers are like the doorbells in a house. They listen for signals from the system or other apps, and they can trigger actions like sending a notification or turning on a light.
* Content providers are like the storage closets in a house. They manage access to data that the app needs to store or retrieve, like photos or contact information.

Using these various components, we can develop various types of apps. One more thing to mention, Each component in Android has its own life cycle, which is a crucial concept in the Android framework.
*Stop wait a minute what is life cycle?*
Let's take the activity component as an example, imagine you are playing a game on your smartphone. When you start the game, the activity representing the game is created. This is called the "onCreate" stage of the activity. While you are playing the game, the activity goes through different stages, such as "onStart", "onResume", and "onPause". These stages represent the different states the activity can be in, depending on what you are doing with the game. For example, if you receive a phone call while playing the game, the activity will go into the "onPause" stage, because the game is no longer in the foreground. Once you finish playing the game and close it, the activity is destroyed. This is called the "onDestroy" stage.
To understand more Here's a picture of the life cycle of the activity component:
![](https://i.imgur.com/jbRYH7A.jpg)

We have different components that make up an app, but *how do they talk to each other?* That's where intents come in. It's like sending a message or a request to someone. Intents allow you to specify what action you want to perform and what data you want to send or receive. For instance, let's say you have Activity X that needs to communicate with Activity Y. In this case, you would use an explicit intent, which is a specific type of intent that's used to specify which action you want to do within an app's components. It's important to note that while intents are responsible for specifying the action to be taken, the actual communication between different components is handled by a low-level mechanism known as **binder**. We will dive deeper into the details of binder in the upcoming chapter, but for now it's crucial to have a clear understanding of the role of intents. There are two types of intents:

* **Explicit Intent**: An explicit intent is used to start a specific component, such as a specific activity or service, within your own app. It's like telling your phone exactly which app you want to open and which screen you want to see within that app. This type of intent is typically used within your own app to navigate between different screens or to start a background service.

* **Implicit Intent**: An implicit intent is used to request an action from another app on the device. It's like telling your phone what you want to do, and letting it figure out which app can do it for you. For example, if you want to share a photo from your gallery app, you can use an implicit intent to request that another app, like Facebook or Instagram, handle the sharing for you. The system will display a list of apps that can handle the request, and you can choose which one to use.

I have been using most of these communication methods for almost six years in my phone, but I didn't know they were called intents. It's quite fascinating, to be honest. If you're interested, you can learn more about intents by visiting the official documentation at [@Official_Docs](https://developer.android.com/guide/components/intents-filters)

Here's an example of an explicit intent: 
```java
{
	...
   Intent i = new Intent(this, SecondActivity.class);
   // this is like the arg0
   i.setData("You can follow me @ironbyte.me");
   // adding more data means adding more args so that's the arg1
   i.putExtra("arg1", "more and more...");
   // We call the SecondActivity and send it this data.
   startActivity(i);
   ...
}
```
This code is creating and starting and intent to the APP.SecondActivity component, and sending some data to it.

Here's an example of an implicit intent:
```java
{
	...
   String url = "https://www.ironbyte.me";
   Intent i = new Intent(Intent.ACTION_VIEW);
   i.setData(Uri.parse(url));
   startActivity(i);
   ...
}
```
This code is creating an intent with an action *ACTION_VIEW* which is used to view the data that is specified in the intent. In this case, the data being viewed is a URL for my blog that is specified using the setData method of the intent with a Uri object. Finally, the startActivity method is called with the intent as a parameter to start the activity and search for an app that can handle the action ACTION_VIEW and display my blog, in this case the default web browser of the device could handle that.

A question popped up in my mind about implicit intents - how do other apps know that they can handle a particular intent type, such as "X"? For instance, going back to the example of opening my blog homepage, how does it know that it can handle this kind of intent? The answer is through the implementation of *Intent Filters*.

* **Intent Filters**: Intent filters are basically used in Android to allow an app to receive intents that are sent by other apps or system components. An intent filter is a set of rules that specify the types of intents an app can respond to. It is like a gatekeeper that filters out intents that do not match the specified criteria. Intent filters are used in Android to allow an app to receive intents that are sent by other apps or system components. An intent filter is a set of rules that specify the types of intents an app can respond to. It is like a gatekeeper that filters out intents that do not match the specified criteria. For example, if an app wants to receive intents related to displaying images, it can set up an intent filter that specifies the action "ACTION_VIEW" and the data type "image/*". When another app sends an intent with the same action and data type, the system checks if any apps have set up an intent filter that matches the criteria. If the filter matches, the intent is delivered to the app that set up the filter. To apply an intent filter to a specific component, such as an activity, you can simply add it to the app's manifest(Don't worry i will explain it in the practice) of the app in the corresponding component section.
```xml
<activity android:name=".ImageDisplayActivity">
    <intent-filter>
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
        <data android:mimeType="image/*" />
    </intent-filter>
</activity>
```

I hope you've gained a lot of knowledge about component communication in an APP. Now, let's shift our focus to a concept known as API levels.

* **API Levels**: API levels are a way of categorizing the features and capabilities of an operating system or software development kit (SDK). In the case of Android, an API level refers to a specific version of the Android operating system that provides a certain set of functionality to developers. Each Android version is assigned a unique API level, starting from 1 and increasing with each subsequent release. Developers can target specific API levels in their apps to ensure that the app can take advantage of the features and capabilities of that particular Android version, while still maintaining compatibility with older versions. 

Here is an image displaying several API levels, just a little note that it is not updated to the latest version so that's why you need to check the [@offical_Doc](https://apilevels.com/).

![](https://i.imgur.com/jzueQPX.png)

Now, a quick question *is it always better to develop apps using the latest API level?* 
The answer is that it's not always better to use the latest API level when building your project. The latest API level may have new features and improvements, but it may also have bugs and incompatibilities with older devices. One thing to mentions is that it's important to consider your target audience and their devices when choosing the API level for your project. If your target audience is using older devices with lower API levels, you may need to use an older API level to ensure compatibility. On the other hand, if your target audience is using newer devices with higher API levels, you may want to use a newer API level to take advantage of the latest features and improvements.

Alright, let's put our theory into practice! We can start by downloading Android Studio from the [official website](https://developer.android.com/studio/) and create our first app. Although I'm not an expert in Android app development but i'm quite good at dealing with the code since i have a great foundation @java. For the sake of showcasing the different components, we can create a simple app that displays a "Hello, World!" message when a button is pressed. This way, we can explore where the different components we've discussed live in the app. To setup the android sudio and create a new project you can follow a youtube tutorial or something it's easy.

I've created an Android project for a simple app that includes two buttons - one that displays "Hello World!" in a snackbar and another button that allows the user to navigate to other *fragments*.

![](https://i.imgur.com/F5ecjXX.png)

Here's an image of the app's user interface. We'll dive into the code behind it step by step. But first, here's the project's hierarchy:

![](https://i.imgur.com/TiiFGhF.png)

I will walk you through the app's navigation and explain how it works. Don't worry, it's a simple app. Afterwards, we will examine the app's manifest file. It's worth noting that the app's package name, "com.example.learn", serves as its unique identifier as we speaked above in the package name part.

Let's dive into the main activity code: 
```java
public class MainActivity extends AppCompatActivity {

    private AppBarConfiguration appBarConfiguration;
    private ActivityMainBinding binding;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        binding = ActivityMainBinding.inflate(getLayoutInflater());
        setContentView(binding.getRoot());

        setSupportActionBar(binding.toolbar);

        NavController navController = Navigation.findNavController(this, R.id.nav_host_fragment_content_main);
        appBarConfiguration = new AppBarConfiguration.Builder(navController.getGraph()).build();
        NavigationUI.setupActionBarWithNavController(this, navController, appBarConfiguration);

        binding.fab.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View view) {
                Snackbar.make(view, "Hello World!", Snackbar.LENGTH_LONG)
                        .setAction("Action", null).show();
            }
        });
    }

    @Override
    public boolean onCreateOptionsMenu(Menu menu) {
        // Inflate the menu; this adds items to the action bar if it is present.
        getMenuInflater().inflate(R.menu.menu_main, menu);
        return true;
    }

    @Override
    public boolean onOptionsItemSelected(MenuItem item) {
        // Handle action bar item clicks here. The action bar will
        // automatically handle clicks on the Home/Up button, so long
        // as you specify a parent activity in AndroidManifest.xml.
        int id = item.getItemId();

        //noinspection SimplifiableIfStatement
        if (id == R.id.action_settings) {
            return true;
        }

        return super.onOptionsItemSelected(item);
    }

    @Override
    public boolean onSupportNavigateUp() {
        NavController navController = Navigation.findNavController(this, R.id.nav_host_fragment_content_main);
        return NavigationUI.navigateUp(navController, appBarConfiguration)
                || super.onSupportNavigateUp();
    }
}
```
As you can see this code represents the main activity of an Android app and it's putting too much light upon the life cycle of the activity component. It extends the AppCompatActivity class and overrides some of its methods. In the onCreate() method, the ActivityMainBinding class is used to inflate the layout of the activity, which contains a toolbar, a FloatingActionButton (FAB) and a NavHostFragment. The setSupportActionBar() method is called to set the toolbar as the activity's app bar. The NavController class is used to navigate between the fragments hosted by the NavHostFragment. The Navigation.findNavController() method is called to retrieve the NavController instance associated with the NavHostFragment. An AppBarConfiguration instance is also created, based on the navigation graph associated with the NavController. The NavigationUI.setupActionBarWithNavController() method is then called to enable the app bar to navigate to the fragments. The FloatingActionButton is assigned a click listener in the onClick() method. When clicked, it shows a Snackbar with the message "Hello World!". The onCreateOptionsMenu() and onOptionsItemSelected() methods are used to inflate the options menu and handle item selections, respectively. Finally, the onSupportNavigateUp() method is overridden to navigate up to the parent activity when the up button in the app bar is pressed.

Here is an illustration of how the message button works:

![](https://i.imgur.com/refKZNz.png)

It's important to note that this app only has one activity, which includes two fragments. Although I haven't mentioned it before, a Fragment is essentially a modular section of an activity with its own lifecycle, input events, and views. It can be added or removed from an activity dynamically and can be seen as a reusable part of an activity. Using fragments can make user interfaces more flexible and modular. They allow fragments to communicate with each other, share data with the parent activity, and have their own layout files and UI elements. Meanwhile, the button in the middle is used to let the user naviagte from the first fragment to the second one.

![](https://i.imgur.com/YckuFMX.png)

Now it's time to take a look at the manifest file:

![](https://i.imgur.com/2ksXmNR.png)

The app's manifest file contains some important details to consider. One is that the app's target API level is set to 31, which means that it is designed to run on devices with an API level of 31 or higher. Additionally, the app has an intent filter on its activity component, which means that this activity serves as the entry point for the app. Specifically, the activity has the action.MAIN, and the category is set to android.intent.category.LAUNCHER, which indicates that this is the activity that should appear as the top-level entry point in the app launcher.

We hit the end of the fundamentals chapter, where I have covered most of the foundational components of the Android framework. Don't hesitate to reach out to me if you have any questions! We're all on this journey together. You can find me on Discord at @IronByte#0855 or connect with me on [LinkedIn](https://www.linkedin.com/in/med-ali-ouachani/). And hey, make sure to follow me on Twitter too [@ir0nbyte](https://twitter.com/Ir0nbyte) . See you in the next chapter!.
