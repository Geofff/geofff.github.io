---
layout: post
title:  "FM Radio Transmitter"
date:   2017-01-22 18:55:36 +0800
categories: jekyll update
tags:
- hardware
---
<link rel="stylesheet" href="{{base_url}}/css/main.css">
This project orignated simply because my car doesn't have an auxillary audio input on its radio. 
Wanting to be able to play music off my phone, I looked at portable FM radio transmitters, which are all the rage lately. 
A quick search online shows you can find these ubiquitously and cheaply, and so I thought, how hard could it be to make my own?
As it turns out, harder than first thought. 

{% include image.html
            img="/images/belkin-transmitter.jpg"
            caption="A Belkin FM transmitter" %}

While FM radio encoding is more complex than AM, it is still a fairly simple encoding. 
In a nutshell, a high signal voltage corresponds to a high frequency, and a lot signal voltage corresponds to a low frequency.
Of course, exactly how it corresponds is the key to it all.

FM predominantly exists in the frequency range from 87.5Mhz to 108Mhz, depending on location and country. 
Typically, each station will use a +/-400kHz region.

In order to broadcast FM then, we need to be able to generate a frequency somewhere in the 85.5-108Mhz region, and be able to make very small adjustments to this frequency.
The chip I used to do this is a simple VCO, the MAX2606EUT+T. This chip is a very basic 6 pin IC, and has a pair of differential outputs, perfect for driving an antenna with.

The chip is suitable for a very large frequency range, the MAX2606 version selected allows 70-150Mhz, but the rest of the range can allow from 45Mhz up to 650Mhz.

The chip also allows you to have a fixed frequency to use as a baseline, by having an inductor as a tuner. We can calculate the value of this inductor based off of the diagram given.

{% include image.html
            img="/images/Induction-V-Frequency.PNG"
            caption="Inductor value vs Baseline frequency" %}

An inductor value of 390nH gives us a base frequency of approximately 106Mhz. 

We can then finetune the frequency using the tune pin, with a voltage from 0 to VCC. 
This tuning signal is going to be comprised of two parts, first is the output from a potentiometer, used to select which channel to transmit on, and the second is the audio signal being transmitted.

Our original audio signal is from a stereo audio source, so we first have to combine them into a mono signal. Thankfully, this is fairly easy, and since we are working with low current and high resistance, we can sum the channels by forming a simple resistive network.


{% include image.html
            img="/images/r-summer.PNG"
            caption="2-Channel summer" %}

We also want to be able to control the volume of this signal, which we can do by making a simple voltage divider using a potentiometer.

Our frequency controller is also a simple voltage divider, and we can sum these two values in the same way as before. 

From all these, we can construct our whole circuit. It is directly as described, with additional smoothing capacitors as specified by the MAX2606's datasheet.


{% include image.html
            img="/images/schematic.PNG"
            caption="The completed Schematic" %}

All the parts used in the project were fairly simple, and had many variants, so it is entirely possible to construct the project on a breadboard or protoboard, yet I chose to create a PCB anyway.

The circuit was fairly simple to lay out, and I had a whole 5cm x 5cm to work with, so space wasn't an issue. 
Capacitors and inductors had SMD parts chosen, while resistors remained through hole, as that is what I had on hand. The battery connector is left as 2 solder pads, so that any onnector can be used. 
I ended up using a simple 2 AA-battery holder, which has roughly 3V. 
The antenna is also a simple solder pad, so that anything can be used, I used a simple length of wire, since range did not matter.
Mounting holes were added, for ease of adding a case.

The greatest problem arose during the design of the PCB, the stereo audio connector I had did not have any footprint available, and in fact did not even have a datasheet that I could find. 
In the end I had to completely construct the footprint from scratch, using a ruler and a pair of calipers to measure the relevant distances. 
Yet when I receieved the PCB, the audio jacks would not fit, even though the pins completely lined up with the holes. 
I had overlooked the simplest part of all, the pins were not round, they were long, and wouldn't fit in a round hole.
Thankfully, a dremel and a careful hand would make the pins fit.


{% include image.html
            img="/images/PCB.PNG"
            caption="The finalised PCB" %}

A recent feature of KiCad I discovered is the 3D viewer, in which you can attach 3D models to your schematic parts, and it automatically generates a 3D model. This can be used to create a rough idea of the final product.


{% include image.html
            img="/images/FM-Transmitter-1.png" %}

{% include image.html
            img="/images/FM-Transmitter-2.png" 
            caption="The 3D render" %}


After a few weeks waiting, the PCB arrived, which ended up looking great.

{% include image.html
            img="/images/PCB_photo.jpg"
            caption="The produced PCB" %}

And after assembly.


{% include image.html
            img="/images/finished-1.jpg" %}

{% include image.html
            img="/images/finished-2.jpg"
            caption="Soldered and completed boards" %}



Not bad! Pretty close to the rendered 3D model. 

Next up is testing. With the use of a USB Software Defined Radio, I was able to listen in easily, and quickly determine where the transmitter sits on the frequency scale.
I play music over the auxillary port, and sure enough, I can hear it on the computer. 

After all of this, only one flaw existed: the tuning potentiometer. It turns out that while the potentiometer gives a great range of values, it's far too sensitive. 
A slight nudge of the potentiometer results in the frequency being bumped a few hundred Khz. 
This is a relatively easy fix, all that needs to be done is to replace the potentiometer with one that has the same resistance, but a higher number of turns.  

Overall the transmitter works well, and has fairly high quality audio, and I still use it regularly.