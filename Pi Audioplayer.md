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
What you need to recreate this project:  

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

First:
 
1. Flash the Raspberry OS onto your SD Card using something like the PI imager, Rufus, Balena Etcher

2. Get a monitor that supports HDMI input and plug in a HDMI cable into both the back of the monitor and into the Raspberry Pi

3. Wait for the OS to load and open terminal. If not already installed, install pygame and rpi.gpio

   - Check whether it is installed already using (pip3 show pygame) first. If not, To install pygame, type (sudo apt install python3-pygame)
   
   - Do the same with rpi.gpio (pip3 show RPi.GPIO) first. If not, To install RPi.GPIO, type (sudo apt install python3-rpi.gpio)

4. Copy this python code and save it as a .py file using Thonny(Built in python IDE)

```python
import RPi.GPIO as GPIO
import subprocess
import time
import os


PLAY_PAUSE_BUTTON_PIN = 18
NEXT_TRACK_BUTTON_PIN = 23
music_folder = "/home/dylan/Downloads/Songs"  # Path to folder with .mp3 files


is_playing = False
current_track_index = 0
tracks = []
vlc_process = None


GPIO.setmode(GPIO.BCM)
GPIO.setup(PLAY_PAUSE_BUTTON_PIN, GPIO.IN, pull_up_down=GPIO.PUD_DOWN)
GPIO.setup(NEXT_TRACK_BUTTON_PIN, GPIO.IN, pull_up_down=GPIO.PUD_DOWN)

def load_tracks():
    """Load all .mp3 files from the music folder."""
    global tracks
    tracks = sorted(
        [os.path.join(music_folder, f) for f in os.listdir(music_folder) if f.endswith(".mp3")]
    )
    if not tracks:
        print("No .mp3 files found in the music folder.")
        exit(1)

def start_vlc():
    """Start VLC in RC mode."""
    global vlc_process
    vlc_process = subprocess.Popen(
        ["cvlc", "--intf", "rc"],
        stdin=subprocess.PIPE,
        stdout=subprocess.DEVNULL,
        stderr=subprocess.DEVNULL,
        text=True
    )

def play_track(index):
    """Send command to VLC to play a specific track."""
    global is_playing
    track_path = tracks[index]
    print(f"Playing: {os.path.basename(track_path)}")
    vlc_process.stdin.write(f"add {track_path}\n")
    vlc_process.stdin.flush()
    is_playing = True

def toggle_play_pause(channel):
    """Toggle between play and pause."""
    global is_playing
    if is_playing:
        print("Pausing playback...")
        vlc_process.stdin.write("pause\n")
        vlc_process.stdin.flush()
        is_playing = False
    else:
        print("Resuming playback...")
        vlc_process.stdin.write("pause\n")  
        vlc_process.stdin.flush()
        is_playing = True

def next_track(channel):
    """Skip to the next track."""
    global current_track_index
    current_track_index = (current_track_index + 1) % len(tracks)
    print("Skipping to next track...")
    play_track(current_track_index)


load_tracks()

start_vlc()


play_track(current_track_index)


GPIO.add_event_detect(PLAY_PAUSE_BUTTON_PIN, GPIO.RISING, callback= toggle_play_pause, bouncetime=100)
GPIO.add_event_detect(NEXT_TRACK_BUTTON_PIN, GPIO.RISING, callback= next_track, bouncetime=100)

try:
    print("Press the play/pause button to toggle playback.")
    print("Press the next track button to skip to the next track.")
    while True:
        time.sleep(0.1)  # Keeps the script running
except KeyboardInterrupt:
    print("Exiting...")
finally:
    if vlc_process:
        vlc_process.terminate()
    GPIO.cleanup()
 ```


  
  

