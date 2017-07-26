---
title: "1Password on Linux"
categories:
  - security
tags:
  - 1password
  - linux
---

Lately, my long-lasting love for Apple products has been fading. I ditched my iPhone 6+ for a [OnePlus One](https://oneplus.net/) and I relegated my MBP to mostly photo-editing tasks. I'm typing this from a fresh installation of [Xubuntu 14.10](http://xubuntu.org/), which I have been using in the past and I tend to prefer  to the heavy-weight Ubuntu.

My totally most used app in the Apple eco-system is [Agile Bits 1Password](https://agilebits.com/onepassword).In fact, what I miss most in my new Android phone is the fingerprint reader integration with 1Password. I have to type my rather long and cumbersome password every time I need to look for a piece of encrypted info. Agile Bits doesn't support Linux at all. For Linux users, they offer a web interface called [1Password Anywhere](https://guides.agilebits.com/1password-mac/5/en/topic/1passwordanywhere). It's Javascript based and runs locally. Sub-optimal, if you ask me.

![Silvrback blog image](https://silvrback.s3.amazonaws.com/uploads/c02a65b5-0229-4864-9a42-06b648f70393/1password_4_large.png)

So, during the Xubuntu setup process, I started googling around for some recent solutions to running 1Password on Linux and I stumbled on the awesome, little [repo](http://hg.icculus.org/icculus/1pass/) of [Ryan Gordon](https://plus.google.com/+RyanGordon/posts/ZFyb9TQS9zB). Ryan has hacked together a Lua script, named **1pass**, which can decrypt the 1Password keychain and show whatever info you store in 1Password in a very minimalistic GUI.

The setup wasn't exactly straightforward, but eventually I got it to work. Here is  quick rundown of the steps required to get going with 1pass.

InstaIl [Mercurial](http://www.selenic.com/mercurial/), to fetch the 1pass repo.

```
sudo apt-get install mercurial
```

Clone Ryan's repo, 1pass:

```
hg clone http://hg.icculus.org/icculus/1pass/
```

Install some dependencies:

```
sudo apt-get install libgtk2.0-dev libxtst-dev
```

Run the build:

```
cd 1pass
./build.sh
```

If 1pass built without errors, copy (or better symlink from Dropbox) the keychain in 1pass folder.

 ```
mkdir 1Password
ln -s ~/Dropbox/xx/yy/zz/1Password.agilekeychain/ 1Password/1Password.agilekeychain
```

You can finally try out 1pass, by typing:

```
./1pass
```

The script welcomes the user with a rather cryptic message:

```
Now waiting for the magic key combo (probably Alt-Meta-\) ...
```

The "magic combo, on my Lenovo X220, is  "Windows Key+Alt+\"
I had to figure it out by checking out a __[keyhook.c](http://hg.icculus.org/icculus/1pass/file/5155ff0e6d3d/keyhook.c)__ file, located in the project's root.
For the binding to work, you need to press the keys in the right sequence, which is:

```
Alt -&gt; Win -&gt; \
```

 By pressing the keys at the same time, or the Win key first, will cause the popup to stay well hidden.

This is the password prompt box that pops-up on pressing the "magic combo".

![Silvrback blog image](https://silvrback.s3.amazonaws.com/uploads/a90f6295-4ae1-437a-b3c8-9f5e1cd7182a/1pass_1_large.jpg)

On successful password entry, a floating menu displaying the categories in the keychain should appear. You can then navigate through the categories, select the password, press Enter and the password will be copied in the clipboard.
The key binding works everywhere, so it is possible to access the encrypted info's  menu from the browser or whatever application.

To avoid leaving a terminal tab opened just for 1pass, I go like this:

```
./1pass &amp; exit
```

Edit:

This post was picked up by Hacker News. As usual, lots of interesting pointers. Follow the conversation here: <https://news.ycombinator.com/item?id=9091691>