---
title: Pi AudioPlayer
layout: home
---
This is an Grade 11 project done for my Computer Engineering class with my partner Ron.
<hr>
Description: This audioplayer uses a Raspberry pi, mine is a 4 model B, a python script that uses vlc as a the music player and a breadboard for circuit work.
<hr>
Some History first:

- At first, we got a Raspberry Pi (RPi for short) named "Michael" from our teacher Mr.Andrade but turns out, it didn't work because its SD card was not showing up as a storage device during the Raspberry Pi imager process. The reasoning behind it not showing up was something I am not sure. However at home, I had a Raspberry Pi 4 model B that already had a 32 GB SD card with the OS on it already so I brought it to school and we used that instead meaning we bypass the "imager" process and it was named Michael 2.0.

- Me and my partner then tried installing Volumio (An operating system made to be controlled headless(no monitor) through the Pi's IP address on a web interface on a seperate device and specifically designed for audio and music playing anywhere) We realized that even though I could access the web interface(IP address worked) We need another monitor and another Raspberry PI because only 1 SD Card can be inserted into the Raspberry Pi at a time and a SD card can't more than 1 operating system on it. Switching between each SD card did not work because we need to be on Volumio OS to run the audio player but also need to be on the Raspberry Pi OS at the same time to run the python script in the first place. Impossible to do using 1 monitor and 1 Raspberry Pi. So we scratched that idea and used Raspberry PI and VLC to complete it.

- We made a python script in Raspberry Pi's OS to run directly in the OS itself which performed almost the same function but did not require another monitor or Pi.
<hr>
What you need to recreate this project:  

-  16 or 32 GB micro SD card

-  A Raspberry Pi

-  A monitor that supports HDMI

-  HDMI Cable

-  USB-C to power

- Male to Female jumper wires

- Regular jumper wires

- Breadboard  

- 5V power supply  

- 2 push buttons  

- A python script to read button inputs

- Speakers with an audio jack or headphones
<hr>

First:
 
1. Flash the Raspberry OS onto your SD Card using something like the Pi imager, Rufus, Balena Etcher

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

# Pin configuration
PLAY_PAUSE_BUTTON_PIN = 18
NEXT_TRACK_BUTTON_PIN = 23
music_folder = "/path/to/your/folder"  # Path to folder with .mp3 files

# Playback state
is_playing = False
current_track_index = 0
tracks = []
vlc_process = None

# GPIO setup
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

# Start playing the first track
play_track(current_track_index)

# Detects button presses with debounce
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
5. Grab your breadboard, buttons, jumper wires, power supply

 - Grab two female to male jumper wires and connect the male to 1 leg of the button, and the female to pin 18 on the Raspberry PI board (This is your "Play/Pause" button. (You can type (pinout) in the PI's terminal to see the pin configuration for your Raspberry Pi). Do the same with the other button but instead of the female side connected to pin 18, connect it to pin 23 (This is your "Next Track" button).

 - Next, grab 2 regular jumper wires(not male to female) and connect one side to ground and one side to another leg on the button. Do this for both buttons.
 
 - Resistors are unnecessary because the code has internal pull down resistors
 
```python
GPIO.setup(PLAY_PAUSE_BUTTON_PIN, GPIO.IN, pull_up_down=GPIO.PUD_DOWN)
GPIO.setup(NEXT_TRACK_BUTTON_PIN, GPIO.IN, pull_up_down=GPIO.PUD_DOWN)
```
6. Get some speakers with a audiojack ( Most commonly 3.5 mm but can be any) and plug it into the Raspberry Pi and download your favorite mp3's, store them all in one folder and set the path to that "folder".

7. Enjoy! Nice and easy, Right? You can always add more features and buttons if you want but this is just a base to at least get it working, Modify the code however you want if it doesn't work the way it is intended.

  
  

