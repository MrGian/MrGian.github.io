+++
title = "How and why I made an home server from an old laptop"
date = "2022-07-28"
+++

![Arch Meme 1](/images/archmeme_1.png)

# Why

There are many reasons, but the biggest of all are **security** and **privacy**.\
Everyone thinks **cloud** is a magical thing where you can freely put your data, images and documents,\
but cloud is just a fancy word to describe **someone else computer**.\
Those computers are servers owned by companies that don't care about you, this make them the real owners of **your** data, meaning they can do what they want with it.

So why would you hand all of your data to someone you don't know? Why would you trust them? Why would you think they are doing a great job protecting your data?
Why would you let them use your data to make money?\
The answer is simple: **you shouldn't**.

Well, then what's the solution? **Self-host** your own cloud!

# How

## Hardware

You know that old useless PC that everyone has in the basement? Well, that's not useless anymore!

In my case I had an old Sony Vaio laptop with a dual core Intel Pentium and 4GB of RAM, it may sound like that's not a lot, but it's plenty of power for an home server.\
I only replaced the hard disk with a faster SSD, and that is literally the only thing I had to buy.

The first step was to tear it down, I wanted my home server to be as small as possible, so I had to remove everything that wasn't needed: monitor, case, DVD reader, battery, USB ports, Wi-Fi adapter and hard disk.

Then I had to make a case of some sort, and with the help of a friend we made an open case using scrap wood and some rivets, made some holes in it, then we [painted it black](https://www.youtube.com/watch?v=O4irXQhgMqg).

This is the result (don't mind the dust please)

![Home Server 1](/images/homeserver_1.jpg)

![Home Server 2](/images/homeserver_2.jpg)

Do you like it? Me neither, but it does its job.

## Software

### Modem configuration
![Modem](/images/modem.png)
By default, for security reasons, modems block every incoming connection to your IP.\
But when you host an home server you need incoming connections to get forwarded to your server. This is known as **port-forwarding**.\
I was lucky because my modem lets me choose a **DMZ host**, where DMZ means "**demilitarized zone**", it creates a subnetwork where the choosen host is the only one
exposed to incoming connections, also serving as protection for the LAN network.\
Just what I needed, I've choosen my server as DMZ host and voilà, every incoming connection to my IP on every port gets forwarded to my server.

### Dynamic DNS
My internet provider doesn't let me get a **static IP**, so the solution was to setup some kind of dynamic DNS.\
In practice I just made a script that uses Gandi API (my domain registar) to update the DNS records on the fly, then I used **systemd** to run the script every hour.\
In this way even if the modem gets rebooted with a **new IP** I'm still able to connect to my server without knowing what the new IP is.

### Operating System
![Arch Meme 2](/images/archmeme_2.png)

**I use Arch btw** \
So why not to use it on my server!\
I wanted to have a similar environment as my main PC so Arch Linux was the way to go.

Some say that Arch Linux is not suited for a server, because of its rolling release nature it is considered unstable.\
I think that's not true, having the latest version of your software doesn't neccessarily means unstability, but surely means newer features and better security (and that's crucial in a server).

### Docker
When hosting a home server Docker is almost a must.

Docker uses OS-level virtualization to run software in **containers**, each program runs in its isolated environment, with its own configuration.\
This means you don't have to configure your system to run your programs, so you don't risk other programs to stop working because of such configuration.

I was blown away by its simplicity, there is a docker image for almost every software you need, just add it to your docker compose file and you are good to go.\
Almost nothing to configure.

But it's not always the answer, when you have special needs, it's better to install software the good old way.

### Nextcloud
![Nextcloud](/images/nextcloud_1.png)

Nextcloud is the most important suite of software on my home server.\
I would describe as a **Google services alternative**, with the **BIG difference** that you host it yourself.

Its main feature is **storage**, I use it to sync files between my devices thanks to its client applications, available for mobile and desktop.\
It has a nice interface to browse your files and pictures, and you can configure it for auto-upload.\
This is very convenient on mobile, I take a photo and it get automatically sent to my server.

Even though storage is Nextcloud main feature, it isn't the only one.\
There are **plenty of apps** you can install on your Nextcloud instance, most of them work as their Google counterpart and they get synced across your devices.

I personally use:
- **Photos**, a nice interface to browse your pics
- **Contacts**, to store and sync contancts
- **Calendar**, to store and sync events
- **Notes**, to take and sync notes
- **Passwords**, a simple password manager
- **Talks**, it works just like Teams, chat, video and audio calls

### Invidious
![Invidious](/images/invidious.png)

As you have already understood I try to avoid using Google services at all costs, but what about YouTube?\
How I watch YouTube videos without using YouTube at all?\
I use Invidious, an open source alternative front-end to YouTube.

Other than basic features like subscriptions and playlists, it also lets you **download videos** and it is able to **block ads**.

### Nginx
This website is hosted on my home server, as matter of fact when you visit my website you are actually connecting to my home network and downloading data from my server.\
I use Nginx as web server to serve this website and as reverse proxy for various services.

## Conclusion
There are much more things to do with an home server, like using it a **media server** or as an **home assistant** if you have a domotic house.\
There are infinite possibilities, but all that aside the most important thing is that **YOU** and only you are the one in control of **YOUR** own data and the only one responsible for it, no one else.

And that's it! Let me know what you think or if you want a step-by-step tutorial on how to make you own home server.

You can contact me using the links in the home page.

See you soon!
