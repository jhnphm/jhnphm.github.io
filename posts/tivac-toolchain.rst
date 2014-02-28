.. title: Thesis: Tiva-C Toolchain 
.. slug: tivac-toolchain
.. date: 2014/02/08 15:32:54
.. tags: thesis,tiva-c,arm,xtools,xbh
.. link: 
.. description: 
.. type: text

So I've not been working consistently on my thesis as I should. As
previously mentioned on my old `blog`_, I was considering running
everything directly off the Raspberry Pi and using Xenomai as the
real-time OS in order to keep the jitter low.

After some discussions with my professor, we decided that it would
drastically simplify things if the realtime measurement part was
assigned to a microcontroller, which also has ADCs which the Pi doesn't
have. This essentially relegates the Pi to being a NIC and a data
storage device. There are ethernet interfaces for microcontrollers,
however they tend to cost almost as much as the Pi itself.

We are looking at using the `Tiva-C`_ series microcontroller from Texas
Instruments which has the ability to do 2 megasamples per second, and
runs at 80MHz, for the low cost of 12$. What we actually have on hand is
the LM4F120XL which is essentially the same thing before the series was
rebranded, but without PWM and USB OTG.

The ADCs on the microcontroller require buffering due to the rather low
2.5kOhm impedence. This will be addressed later. In the meantime, we
intend to use the i2c ADCs already on hand.

In order to build the toolchain, I downloaded the latest version of
`crosstool-ng`_ and built it from source, using a `post`_ by Todd Elliot
on the TI forums as a guide. However, it doesn't seem necessary to
install OpenOCD from source on Ubuntu 13.10 for the LM4F120XL, and the
sample config recommended isn't optimal for the microcontroller (doesn't
use the hardware floating point). I've posted my configuration on
`github`_.

One also needs to download `TivaWare`_ from Texas Instruments. Once
downloaded it can be unzipped on Linux without running the windows
executable. Run ``make clean`` on the extracted directory,
``patch -Np1 < hard_fp.patch``, then run ``make``. The patch can be
found in my repository above. My configuration enables the hard float
ABI while TivaWare is set to default to soft float.

To start the on-chip-debugger to program the board, ensure that the
permissions to /dev/ttyAC\* are correct. then run
``openocd -f /usr/share/openocd/scripts/board/ek-lm4f120xl.cfg`` for the
LM4F120XL, or
``openocd -f /usr/share/openocd/scripts/board/ek-tm4c123gxl.cfg`` for
the TivaC. ``ek-tm4c123gxl.cfg`` will have to be obtained from
`gitorious`_ and placed in ``/usr/share/openocd/scripts/board``. The
above assumes OpenOCD was installed from a package manager.

As an example, to program, run
``arm-stellaris-eabi-gdb tivaware/examples/boards/ek-tm4c123gxl/qs-rgb/gcc/qs-rgb.axf``

Below is an example session:

.. code:: text

   GNU gdb (crosstool-NG 1.19.0) 7.6-2013.05
   Copyright (C) 2013 Free Software Foundation, Inc.
   License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>
   This is free software: you are free to change and redistribute it.
   There is NO WARRANTY, to the extent permitted by law.  Type "show copying"
   and "show warranty" for details.
   This GDB was configured as "--host=x86_64-build_unknown-linux-gnu
   --target=arm-stellaris-eabi".
   For bug reporting instructions, please see:
   <http://bugs.launchpad.net/gdb-linaro/>...
   Reading symbols from
   /home/john/Downloads/tivaware/examples/boards/ek-tm4c123gxl/qs-rgb/gcc/qs-rgb.axf...done.
   (gdb) target remote :3333
   Remote debugging using :3333
   0x000002d4 in ButtonsPoll ()
   (gdb) monitor reset halt
   target state: halted
   target halted due to debug-request, current mode: Thread 
   xPSR: 0x01000000 pc: 0x00000ba4 msp: 0x20000910
   (gdb) load

   Loading section .text, size 0x2664 lma 0x0
   Loading section .data, size 0x50 lma 0x2664
   Start address 0xba4, load size 9908
   Transfer rate: 12 KB/sec, 4954 bytes/write.
   (gdb) 
   (gdb) monitor reset init
   target state: halted
   target halted due to debug-request, current mode: Thread 
   xPSR: 0x01000000 pc: 0x00000ba4 msp: 0x20000910
   (gdb) c






.. _blog: http://jhnphm.wordpress.com/
.. _Tiva-C: www.ti.com/ww/en/launchpad/launchpads-tivac.html
.. _crosstool-ng: http://crosstool-ng.org/
.. _post: http://e2e.ti.com/support/microcontrollers/tiva_arm/f/908/t/65137.aspx
.. _github: https://github.com/jhnphm/tiva-c_xtools
.. _TivaWare: https://focus-webapps.ti.com/licreg/docs/swlicexportcontrol.tsp?form_type=2&prod_no=SW-EK-LM4F232-2.0.1.11577.exe&ref_url=http://software-dl.ti.com/tiva-c/SW-TM4C/latest/&form_id=154910
.. _gitorious: https://gitorious.org/openocd/openocd/commit/24099b4c144f1c6d1244b8b4d98c0fd69c9ff2fc
