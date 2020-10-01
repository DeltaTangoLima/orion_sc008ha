# Orion SC008HA Hacks
My recorded attempts at hacking the Oriona SC008HA IP camera

### Introduction

I was recently given an Orion SC008HA wiress IP surveillance camera by a friend who had no need for it. Knowing I was into home automation and enjoyed meddling with all things technology, he gave it to me in exchange for some home-made pasta sauces, a handful of Cat 6A ethernet cables, and a motion sensor for the light at his front door. Assuming I can get this working as I want, I consider that a good deal.

This repo simply documents my efforts to attempt to hack the camera so I can use it as a plain old RTSP camera wth my Home Assistant server, and not be tied to the application required to use it out of the box. I'm inspired, in part at least, by the efforts of @ant-thomas, who managed to (successfully) hack a ZS-GX1 camera [here](https://github.com/ant-thomas/zsgx1hacks).

### 2020-07-19 - First look
* Photos of the separated "head" and board can be found [here](./photos)

Externally, this camera mostly resembles the ZS-GX1, pictured [here](./img/zs-gx1.jpg), with the only notable exception being the lack of an ethernet socket on the back. Instead, there is only a single micro-USB socket for power.

Internally, there is no mainboard in the base of the camera. The only items of note in the base at the power cables from the USB socket and the 5V DC motor used for panning the camera.

The "head" of the camera separates with very little effort. With the camera lens pointing at you, gently pull the right side of the "head" casing away, being careful not to pull too far - there is a wireless antenna cable attached to the half that you're removing. Once the two halves are separated, you can use a fine blade screwdrive to gently prise off the antenna cable, and put the now separate casing to one side.

The board and lens bezel can now be removed from the remaining half of the "head" casing. With the wireless chip at the bottom left, we can see the following (clock face references used):

* Realteak RTL8188FTV wireless chip (6 o'clock)
* HiSilicon H13518ERNCV300 camera SoC (12 o'clock)
* 2-wire connector for speaker (1 o'clock)
* 4-wire connector for pan motor (3 o'clock)
* 4-wire connector for tilt motor (4 o'clock)
* 2-wire connector for USB power (5 o'clock)

No GPIO pins, or any other interface pads, appear to be available.

### 2020-10-01 - Attemping to find UART

So I left this on a corner of my workbench for a couple of months or so, then saw a post by @koutto on Reddit, linking to [this](https://github.com/koutto/hardware-hacking) brilliant starter tutorial, on hacking hardware devices.

(Re)inspired, I took a closer look at the camera board, and (using my previous clockface reference) I saw a row of 4 pads at 11 o'clock.  Time to take a closer look...

* Pad 1 (left): using the ground pin on the USB power connector, this pad is GND
* Pad 2: testing with GND pad, this appears to have a constant voltage of ~3.4V
* Pad 3: testing with GND pad, this appears to have a constant voltage of ~3.4V, slightly less than pad 2
* Pad 4: testing with GND pad, this appears to have a low, fluctuating voltage, between ~200mV and ~1.1V

So far, my first thoughts are that these are UART pins:

* Pin 1 = GND
* Pin 2 = 3.3V Vcc
* Pin 3 = likely Tx
* Pin 4 = likely Rx

Time to solder some header pins and hook it up to either a Raspberry Pi or an Arduino board, and see what I get.

Success ! (of sorts) - looks like I had Tx and Rx the wrong way around. Plugging them in to the UART pins on a Raspberry Pi and using screen to monitor the serial interface gives up:
```
hisi-sdhci: 0
PPS:Jul 22 2019 00:22:28 meari_c5    0 


please input password::
```

So, to recap, the pins are actually:

* Pin 1 = GND
* Pin 2 = 3.3V Vcc
* Pin 3 = Rx
* Pin 4 = Tx

Now to try brute forcing the password, which will likely be a nuisance, as the prompt  locks up after what looks like a 1 second timeout, unless you hit enter to get the password prompt.  More to come...

### 2020-10-01 (update 2) - Gaining access

After trying to brute force using a few lists I found around the place, I started researching what detail I could about the device, as there were very few markings on the outside.

Searching around for the prompt (particularly the "meari_c5" part) eventually led me to discover the camera is made by Hangzhou Meari. Even better, I found [this](https://fccid.io/2AG7CMINI8C) FCC ID, with internal photos depicting a board that looks similar to the board in my camera.

My best guess at this stage is that the Orion SC008HA is a new variant of the Meari Mini 8C. Just in case, I grabbed the user manual and internal photos PDFs (you can find them [here](./fcc/)).

Also, when booting up with the reset button pressed, the output is this:

```
hisi-sdhci: 0
PPS:Jul 22 2019 00:22:28 meari_c5    0 
button
cmd:fatload mmc 0 0x42000000 ppsMmcTool.txt 1020
```

Maybe a clue as to something I can do with a file called ppsMmcTool.txt on the microSD card? More searching to do.