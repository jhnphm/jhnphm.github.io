---
layout: post
title: "Resuming Thesis"
date: 2014-02-08 15:32:24 -0500
comments: true
categories: 
---

So I've not been working consistently on my thesis as I should. As previously
mentioned on my old [blog](http://jhnphm.wordpress.com/), I was considering
running everything directly off the Raspberry Pi and using Xenomai as the
real-time OS in order to keep the jitter low. 

After some discussions with my professor, we decided that it would drastically
simplify things if the realtime measurement part was assigned to a
microcontroller, which also has ADCs which the Pi doesn't have. This essentially
relegates the Pi to being a NIC and a data storage device. There are ethernet
interfaces for microcontrollers, however they tend to cost almost as much as the
Pi itself.

We are looking at using the [Tiva-C][tivac] series microcontroller from Texas Instruments
which has the ability to do 2 megasamples per second, and runs at 80MHz, for the
low cost of 12$. What we actually have on hand is the LM4F120XL which is
essentially the same thing before the series was rebranded, but without PWM and
USB OTG.

The ADCs on the microcontroller require buffering due to the rather low 2.5kOhm
impedence. This will be addressed later. In the meantime, we intend to use the
i2c ADCs already on hand. 

In order to build the toolchain, I downloaded the latest version of
[crosstool-ng][ct-ng] and built it from source, using a
[post](http://e2e.ti.com/support/microcontrollers/tiva_arm/f/908/t/65137.aspx)
by Todd Elliot on the TI forums as a guide. However, it doesn't seem necessary
to install OpenOCD from source on Ubuntu 13.10 for the LM4F120XL, and the sample
config recommended isn't optimal for the microcontroller (doesn't use the
hardware floating point). A sample configuration will be posted later. 

This blog post will be updated once I've actually verified that the hardfp
toolchain works.



[ct-ng]: http://crosstool-ng.org/
[tivac]:www.ti.com/ww/en/launchpad/launchpads-tivac.html
