---
title: CSAW 2022 Quals - Anya 
date: 2022-09-15 00:16:30
---
<center>

Anya Writeup

</center>

Before we start you need to download the game from here : [game](https://github.com/IR0NBYTE/binaries/blob/main/Game.rar).

Let's try first to run and the game and play it a little! 

![](https://i.imgur.com/l5IGLoo.png)

As you can see it's a coin game, in which you have to use coins to try to get the flag. 

![](https://i.imgur.com/lWcQpgS.png)

You have 10 chances to get the flag, after you use all of them, the game just give you a another fail screen. 
It's a [unity](https://en.wikipedia.org/wiki/Unity_(game_engine)) game, there like a specific file for unity games that containes the *Assembly* of the game. It's usually called **Assembly-CSharp.dll**, and since it's a C# file we need to Analyse it using **dnspy** decompiler.

Here's what i got when i opened the file using **dnspy** : 

![](https://i.imgur.com/6zuHBI5.png)

Basically what got my interest automatically when i saw the functions were the **start**, **wish**, **succeed**, **fail** and **upload** functions. Maybe we should analyse them !

Start function : 
```C#
private void Start() {
    Debug.Log("Main Logic Starts");
    this.counter = Encoding.ASCII.GetBytes("wakuwaku");
    this.value_masker = UnityEngine.Random.Range(1, 999);
    this.value = this.mask_value(100);
    Debug.Log(Convert.ToBase64String(this.mySHA256.ComputeHash(this.counter)));
}
```
As you can see the start function is the function that is initializing the variables counter, value_masker and mask_value.

Wish function : 

```C#
public void wish() {
    int num = this.unmask_value(this.value);
    if (num < 10)
    {
        this.insufficient_value();
        return;
    }
    num -= 10;
    this.value = this.mask_value(num);
    this.counter = this.mySHA256.ComputeHash(this.counter);
    this.loading.SetActive(true);
    base.StartCoroutine(this.Upload());
}
```

This function is called, when the button of *wish* is pressed. It checks whether you have enough coins to get a chance to roll for the flag.  

Fail function : 

```C#
private void fail() {
    this.loseaudio.Play();
    this.mainpage.SetActive(false);
    this.failure.SetActive(true);
    Debug.Log("Got nothing");
}
```

This is the function that is responsible for showing the fail screen when the game fails to grab the flag. 

Succeed function : 

```C#
private void succeed(string f) {
		this.winaudio.Play();
		this.mainpage.SetActive(false);
		this.success.SetActive(true);
		this.flag.GetComponent<Text>().text = f;
		Debug.Log("Got Anya!");
}
```

This is the function that is responsible for showing the flag in the screen when the game succeeds to grab the flag. 

Upload functions :

```C#
private IEnumerator Upload() {
    WWWForm wwwform = new WWWForm();
    string str = Convert.ToBase64String(this.counter);
    wwwform.AddField("data", str);
    UnityWebRequest www = UnityWebRequest.Post(this.server, wwwform);
    Debug.Log("Posted: " + str);
    yield return www.SendWebRequest();
    if (www.result != UnityWebRequest.Result.Success)
    {
        Debug.Log(www.error);
    }
    else
    {
        this.loading.SetActive(false);
        string text = www.downloadHandler.text;
        if (text != "")
        {
            this.succeed(text);
        }
        else
        {
            this.fail();
        }
    }
    yield break;
}
```

This function is uploading a *hashed* string to a server using **Post** methode and it waits for some result from the server, Hmmm at first I just thought if I just patch the binary and try to make the game behave without failing I might just get the flag.
Things did not turn out like that, I noticed something super guessy in the game : 

![](https://i.imgur.com/XmAl7oF.png)

It's saying that you have 0.1 chances to get the flag, it's guaranteed with 1000 wishes. Basically the idea was to send 1000 requests to the server by clicking at the button 1000 times. But we are programmers we don't do that here, all we did was add a loop that iterates from 1 to 1000 and sends 1000 requests to the server with the hashed word *wakuwaku*, we patched the code and saved the module, saved the file and ran the game again.

The upload code : 

```C#
private IEnumerator Upload() {
    int i; 
    for(i = 1; i <= 1000; i++) {
        WWWForm wwwform = new WWWForm();
        string str = Convert.ToBase64String(this.counter);
        wwwform.AddField("data", str);
        UnityWebRequest www = UnityWebRequest.Post(this.server, wwwform);
        Debug.Log("Posted: " + str);
        yield return www.SendWebRequest();
        if (www.result != UnityWebRequest.Result.Success)
        {
            Debug.Log(www.error);
        }
        else
        {
            this.loading.SetActive(false);
            string text = www.downloadHandler.text;
            if (text != "")
            {
                this.succeed(text);
            }
            else
            {
                this.fail();
            }
        }
    }
    yield break;
}
```

Here's the flag : 

![](https://i.imgur.com/tz9cHdJ.png)

**flag{@nya_haha_1nakute_5amishii}**

