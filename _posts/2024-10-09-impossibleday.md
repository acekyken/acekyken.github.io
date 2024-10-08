---
title: Impossible Day At Recurse Fall 2
layout: post
---
# Impossible Day, 8th of October 2024
I'm currently in a batch at [Recurse Center,](https://www.recurse.org/) where I try to learn at the edge of my abilities (one of the core principles of RC). Today was Impossible Day, a day on which recursers should try to do something way beyond the edge of their abilities, something that feels impossible. The time frame is roughly 5 hours between kickoff and debrief.

In the spirit of learning generously (another principle of the recurse center), I wrote down my process and my thoughts for today. They will be only minimally edited, since it would otherwise not truly reflect what I was able to do in the 5 hours I had.

## Hour 0
In the middle of the night I woke up and had this idea: is it possible to use Bluetooth discovery messages as a means of transporting messages in a large group of people? My idea was something similar to FindMy or the google equivalent I forgot the name of, but capable of transmitting messages. So of course, I got up at 5:30 and started googling the Bluetooth standard and Bluetooth on Android. As it turns out and I had suspected, it is not possible to send just any message over the Bluetooth frequency from an ordinary Android app. My idea to circumvent this was to use something like an Arduino, connect it via USB to the phone and use this to transmit the messages.
The idea of the network is to have some kind of gossip setup, in which each participant sends messages it receives (and is not the intended recipient for) to a subset of all devices it sees. Participants who want to communicate have somehow established a pre-shared key and identifiers (which would be unique to this communication). This ensures that the actual messages transmitted can be encrypted, and the sender and receiver can be anonymous.
Is it possible? I don't know yet. I stopped my research and planned to start on everything after the kickoff.

## Hour 1
I started with digging through my electronics to find something that can talk Bluetooth, and found my ESP32. I also started researching how to control an Arduino via USB from an Android phone, and I think I need to connect it via USB OTG and then probably talk serial. 
I think there are many, many possible first steps in this project. The first one seems to be to list all the parts, or at least all parts I can have a look at first.
- Have the ESP speak "custom" Bluetooth
	- write a small proof of concept and send out a beacon and see if I can see it from my phone or computer?
- Send a command from an Android app to the Arduino via USB
	- write a super simple Android app that can send commands over USB serial 
	- write a simple proof of concept esp project that listens to commands over USB serial
- Have a look at the Bluetooth standard and map out all protocol messages that could be used
- Have a look at mesh networks in order to figure out how to distribute messages without holding large amounts of state

## Hour 2
I managed to flash the ESP32 with the BLE beacon scanner example from the ESP32 library. I skimmed some information about BLE/Bluetooth 4.0, and it has a mesh option, so maybe I should look into that instead? No idea though. My next practical step could be to write a proof of concept android app that reads the beacon scanner output of the ESP. This will be sufficient to prove that the serial connection should work.

I decided to see how the serial connection over USB works by using the [Serial USB Terminal](https://github.com/kai-morich/SimpleUsbTerminal) app from the play store. I only granted the permission to access the device, set up the correct baut rate and there it was, the output of the example scanner. Now, get that working in my own app and figure out if Bluetooth Mesh is a good idea for my project.
## Hour 3
The Bluetooth Mesh standard has 900 pages....
Let me watch a youtube video instead. Watched https://www.youtube.com/watch?v=KoyVpNpadxE , or rather parts of it. I learned that mesh is a closed network and all devices need provisioning in order to obtain the keys to participate. The example given in the video is a large amount of lights and light switches as well as light sensors. New devices are added by being provisioned using an android app. The endpoints use a publish and subscribe pattern to communicate any new state. 
This is not really what I need. I guess I can get around the provisioning by baking in keys into my app, therefore making them public. This probably means that any message integrity has to be added on another layer of my protocol, as well as confidentiality.
I'm not sure yet how the messages look in Bluetooth Mesh, an interesting question would be if I can get a decently obfuscated sender and an anonymous receiver. 

## Hour 4
I had a meeting with faculty to discuss my direction in RC, and afterwards I decided to update my system and reboot, since I had experienced some weird behavior. Of course the update triggered something to be different with my display settings and one of my displays decided to turn itself off and on every 5 seconds. That took me around half an hour to fix as well. 

## Hour 5
Now getting back to it, probably just trying to communicate with the ESP32 from an Android app since I have about half an hour left.

I figured out that a library exists called [usb-serial-for-android](https://github.com/mik3y/usb-serial-for-android), which makes it easy to establish a serial connection with an Arduino-like device. It is also used in the Serial USB Terminal app, as I found out later.
### how to use usb-serial-for-android in a kotlin project
- add `maven(url = "https://jitpack.io")` to settings.gradle.kts
- add `implementation("com.github.mik3y:usb-serial-for-android:3.8.0")` to build.gradle.kts
- sync

The offical debrief started while I was still adding the usb permissions to my manifest file.

## Official End at After Hour 5
I didn't really have anything presentable after hour 5, so I did not manage to convince myself to speak this time. What I had done essentially was figuring out how to run the demo code of BLE on ESP32, how to display that in the Serial USB Terminal app from the play store, and otherwise gained some knowledge of the Bluetooth Standard. This, however, can only be described as very minimal (have I mentioned the 900 pages of Bluetooth Mesh).
I also started implementing the serial connection in an Android app myself, but did not get very far.

## Lessons Learned
I think I did a good job sticking to the theme of impossible day, which is to try to do something that is beyond my capabilities and feels impossible. In that regard, I succeeded, because it was indeed impossible to accomplish in 5 hours. I don't even know yet if it is possible at all. I did learn a little bit about Bluetooth on Android.

I can't help but feel a little sad that I did not manage to get anything worth demoing done. So next time, I would like to pick something that allows me to start writing code right away, so that I can have a better chance of being able to demo something.

## Potential Next Steps

- get serial communication running between my app and the esp
- send a custom inquiry message with the esp
- learn about bluetooth mesh / figure out other ways in which bluetooth can be used for this
- prototype an app plus device