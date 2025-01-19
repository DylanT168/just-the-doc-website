---
title: Pi AudioPlayer
layout: home
---
This is an Grade 11 project done for my Computer Engineering class with my partner Ron.
<hr>
Description: This audioplayer uses a Raspberry pi, mine is a 4 model B, a python script that uses vlc as a the music player and a breadboard for circuit work.
<hr>
Some History first:

- At first, we got a Raspberry PI (RPi for short) named "Michael" from our teacher Mr.Andrade but turns out, it didn't work because its SD card was not showing up as a storage device during the Raspberry PI imager process. The reasoning behind it not showing up was something I am not sure. However at home, I had a Raspberry Pi 4 model B that already had a 32 GB SD card with the OS on it already so I brought it to school and we used that instead meaning we bypass the "imager" process and it was named Michael 2.0

- Me and my partner then tried installing Volumio (An operating system made to be controlled headless(no monitor) through the PI's IP address on a web interface on a seperate device and specifically designed for audio and music playing anywhere) We realized that even though I could access the web interface(IP address worked) We need another monitor and another Raspberry PI because only 1 SD Card can be inserted into the Raspberry Pi at a time and a SD card can't more than 1 operating system on it. Switching between each SD card did not work because we need to be on Volumio OS to run the audio player but also need to be on the Raspberry PI OS at the same time to run the python script in the first place. Impossible to do using 1 monitor and 1 Raspberry Pi. So we scratched that idea and used Raspberry PI and VLC to complete it.

- We made a python script in Raspberry PI's OS to run directly in the OS itself which performed almost the same function but did not require another monitor or PI.
<hr>
What you need:  

-  16 or 32 GB micro SD card

-  A Raspberry Pi

-  A monitor that supports HDMI

-  HDMI Cable

-  USB-C to power

- Male to Female jumper wires  

- Breadboard  

- 5V power supply  

- 2 push buttons  

- A python script to read button inputs
<hr>


  
  

