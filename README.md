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
