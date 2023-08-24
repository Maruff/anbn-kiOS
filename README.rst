anbnPiOS
==========

.. image:: https://github.com/Maruff/anbnPiOS/blob/stable/media/anbnPiOS.png?raw=true
.. :scale: 50 %
.. :alt: anbnPiOS logo

A `Raspberry Pi <http://www.raspberrypi.org/>`_ distribution to display one webpage in full screen. It includes `Chromium <https://www.chromium.org/>`_ out of the box and the scripts necessary to load it at boot.
This repository contains the source script to generate the distribution out of an existing `Raspbian <http://www.raspbian.org/>`_ distro image.

anbnPiOS started as a fork from `FullPageOS <https://github.com/guysoft/FullPageOS>`_, but then joined the distros that use `CustomPiOS <https://github.com/guysoft/CustomPiOS>`_.

Donate
------
anbnPiOS is 100% free and open source and maintained by Esan Maruff. If its helping your life, your organisation or makes you happy, please consider making a donation. It means I can code more and worry less about my balance. Any amount counts.

|paypal|

.. |paypal| image:: https://www.paypalobjects.com/en_US/i/btn/btn_donateCC_LG.gif
   :target: https://paypal.me/bizwear?country.x=QA&locale.x=en_US

Where to get it?
----------------

Official mirror is `here <http://igenss.qa/anbn/>`_

Nightly builds are available `here <http://igenss.qa/anbn/anbnPiOS/nightly/>`_ (currently built on demand)

How to use it?
--------------

#. Unzip the image and install it to an SD card `like any other Raspberry Pi image <https://www.raspberrypi.org/documentation/installation/installing-images/README.md>`_
#. Configure your WiFi by editing ``anbnPiOS-wpa-supplicant.txt`` on the first partition of the flashed card when using it like a flash drive
#. Boot the pi from the SD card
#. Log into your pi via SSH (it is located at ``anbnPiOS.local`` `if your computer supports bonjour <https://learn.adafruit.com/bonjour-zeroconf-networking-for-windows-and-linux/overview>`_ or the IP address assigned by your router), default username is "pi", default password is "raspberry" and change the password using the ``passwd`` command. Consider also changing the vnc password as well by `x11vnc -storepasswd`.

Requirements
------------
* Raspberry Pi 2 and newer or device running Armbian. Older Raspberry Pis are not currently supported.  See `Raspberry Pi <https://github.com/Maruff/anbnPiOS/issues/12>`_ and `Raspberry Pi <https://github.com/Maruff/anbnPiOS/issues/43>`_.
* SD card, 4GB or larger, Class 10. (Early June 2020 was the image size 3GB.)
* 2A power supply


Features
--------

* Loads Chromium at boot in full screen
* Webpage can be changed from /boot/anbnPiOS.txt
    * You can use variable `{serial}` in the url to get device's serialnumber in the URL
* Default app is `FullPageDashboard <https://github.com/amitdar/FullPageDashboard>`_, which lets you add multiple tabs changes that switch automatically.
* Ships with preconfigured `X11VNC <http://www.karlrunge.com/x11vnc/>`_, for remote connection (password 'raspberry')
* Specify a custom Splashscreen that gets displayed on booting process instead of Kernel messages/text

Developing
----------

Requirements
~~~~~~~~~~~~

#. `qemu-arm-static <http://packages.debian.org/sid/qemu-user-static>`_
#. `CustomPiOS <https://github.com/guysoft/CustomPiOS>`_
#. Downloaded `Raspbian <http://www.raspbian.org/>`_ image.
#. root privileges for chroot
#. Bash
#. coreutils
#. sudo (the script itself calls it, running as root without sudo won't work)
#. jq (part of CustomPiOS dependencies)

Build anbnPiOS From within anbnPiOS / Raspbian / Debian / Ubuntu
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

anbnPiOS can be built from Debian, Ubuntu, Raspbian, or even anbnPiOS.
Build requires about 2.5 GB of free space available.
You can build it by issuing the following commands::

    sudo apt install coreutils p7zip-full qemu-user-static
    
    git clone https://github.com/guysoft/CustomPiOS.git
    git clone https://github.com/Maruff/anbnPiOS.git
    cd anbnPiOS/src/image
    wget -c --trust-server-names 'https://downloads.raspberrypi.org/raspios_lite_armhf_latest'
    cd ..
    ../../CustomPiOS/src/update-custompios-paths
    sudo modprobe loop
    sudo bash -x ./build_dist
    
Building anbnPiOS Variants
~~~~~~~~~~~~~~~~~~~~~~~~

anbnPiOS supports building variants, which are builds with changes from the main release build. An example and other variants are available in the folder ``src/variants/example``.

To build a variant use::

    sudo bash -x ./build_dist [Variant]
    
    
Building Using Docker
~~~~~~~~~~~~~~~~~~~~~~
`See Building with docker entry in wiki <https://github.com/guysoft/CustomPiOS/wiki/Building-with-Docker>`_

    
Building Using Vagrant
~~~~~~~~~~~~~~~~~~~~~~
There is a vagrant machine configuration to let build anbnPiOS in case your build environment behaves differently. Unless you do extra configuration, vagrant must run as root to have nfs folder sync working.

Make sure you have a version of vagrant later than 1.9!

If you are using older versions of Ubuntu/Debian and not using apt-get `from the download page <https://www.vagrantup.com/downloads.html>`_.

To use it::

    sudo apt-get install vagrant nfs-kernel-server virtualbox
    sudo vagrant plugin install vagrant-nfs_guest
    sudo modprobe nfs
    cd anbnPiOS/src/vagrant
    sudo vagrant up

After provisioning the machine, its also possible to run a nightly build which updates from devel using::

    cd anbnPiOS/src/vagrant
    run_vagrant_build.sh
    
To build a variant on the machine simply run::

    cd anbnPiOS/src/vagrant
    run_vagrant_build.sh [Variant]

Usage
~~~~~

#. If needed, override existing config settings by creating a new file ``src/config.local``. You can override all settings found in ``src/config``. If you need to override the path to the Raspbian image to use for building OctoPi, override the path to be used in ``ZIP_IMG``. By default, the most recent file matching ``*-raspbian.zip`` found in ``src/image`` will be used.
#. Run ``src/build_dist`` as root.
#. The final image will be created in ``src/workspace``


Remote access
~~~~~~~~~~~~~

Remote GUI access can be archieved through VNC Viewer. Get the IP of you raspberry ``hostname -I`` via SSH. 

The password is ``raspberry`` and is independent of password you have set for your user(s). Change the password by ``x11vnc -storepasswd`` via SSH.


Install Chrome Extensions
~~~~~~~~~~~~~~~~~~~~~~~~~

Press ``ctrl`` + ``t``, it will open a new tab. 

You can either install extensions frome `Chrome Web Store <https://chrome.google.com/webstore/category/extensions>`_ or `install your own extension  <https://support.google.com/chrome_webstore/answer/2664769?hl=en>`_. 

If you which to install your own extension then you can transfer the build files via tools like ``rcp``, ``rsync`` etc.

Example::

    rsync -av <extension-build-folder>/ pi@anbnPiOS.local:extensions/<extension-name>/


Code contribution would be appreciated!
