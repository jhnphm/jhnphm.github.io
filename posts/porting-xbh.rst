.. title: Thesis: Porting XBH
.. slug: porting-xbh
.. date: 2014/02/26 22:54:12
.. tags: thesis,xbh,xbx
.. link: 
.. description: 
.. type: text

As mentioned in a previous `post`_, the goal of my thesis is to extend the XBX
project to cover power and energy measurements. In order to do this, the
eXternal Benchmarking Harness (XBH) needs to be replaced with something that can
provide frequent sampling and sufficient storage in order to measure current and
voltage in order to compute power and energy usage, and record mins and maxes. 

The original XBH was based off of a home automation platform using an `ATMega644`_ ,
which was deemed insufficiently powerful. We originally intended on replacing it
with a Raspberry Pi, but unfortunately it was deemed rather unsuitable for real-time
measurement purposes despite its power, as only Linux variants are supported,
bare-metal programming is difficult, and external ADCs would have to be added.
We decided to relegate the Pi to simply a networking interface for the `Tiva-C`_
board from TI, which can easily run bare-metal code and has a builtin ADC. 

The Tiva-C half of the XBH will be responsible for triggering the start of the
measurement via a GPIO pin, measuring voltage and current while operating, and
sensing completion of the benchmark via  GPIO pin. It will record data and
attach a timestamp and send  the data over USB to the Raspberry Pi.
Pre-processing data to reduce what has to be uploaded over USB may be done on
the Tiva-C

The Raspberry Pi will be responsible for receiving commands via SSH or a web
interface and telling the ARM when to start. It is also responsible for
downloading code to and programming the eXternal Benchmarking Device (XBD). 

Currently the task is to get USB IO between a PC and the stellaris working.

.. _XBX: http://xbx.das-labor.org
.. _post: http://jhnphm.wordpress.com/2013/04/18/thesis/
.. _ATMega644: http://www.atmel.com/devices/ATMEGA644.aspx
.. _Tiva-C: www.ti.com/ww/en/launchpad/launchpads-tivac.html
