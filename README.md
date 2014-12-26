# Introduction

**Embedded Crosswalk Project** (ECP) is the minimal Linux platform that boots a
fully-fledged Web application.

This repository is actually a Yocto layer and groups all the needed components
that form the ECP, featuring mostly Crosswalk application run-time and
Chromium. In particular Chromium is using Ozone-GBM platform and therefore
does not rely on any Linux traditional window system like X11, Wayland, etc,
neither graphics toolkits like EFL, Qt. It's a pretty simple and new software
stack that the main motivation is to leverage the Web platform (HTML5, CSS,
JavaScript and more) as the application platform.

You may want to use the Embedded Crosswalk project as a backbone for creating
a real **Web-based Linux distribution** to enable **IoT (Internet of Things),
Wearables, Digital Signage** or any other embedded use case.

# Contents

  - [Design](#design) - the architecture behind
  - [Howto](#howto) - set up the system environment, build and run
  - [Bugs](#bugs) - what's not working that we need still to catch up and improve
  - [Contributing](#contributing) - help develop and send patches
  - [License](#license)

# Design

![Alt text](https://raw.github.com/tiagovignatti/misc/master/yoctocrosswalkembedded-arch.png "Embedded Crosswalk Project architecture overview")

# Howto

**Important caveat: ECP at the moment works only for x86
(Intel) and modern hardware containing GPU due the Chromium Ozone-GBM
architecture. That said, we don't have plans to extend it to any other category of
devices. For testing, development and deployment we recommend the [MinnowBoard MAX](http://www.minnowboard.org/meet-minnowboard-max/)**.

This guide will help you to build a bootable image with the Embedded Crosswalk Project. Most of
the toolchain needed to build comes from Yocto Poky and it's expected to use
just a few of your system's dependencies. We use Ubuntu 14.04 LTS (Trusty
Tahr) in particular, but there's no reason to not use any other different
system. Besides, make sure you have **at least** 45 GB of disk space to store
all the sources and a few types of images to be built.

Firstly, you will need anyway a couple of your distribution tools and
development packages:

  ```
  $ sudo apt-get install gawk wget git-core diffstat unzip texinfo \
gcc-multilib build-essential chrpath libsdl1.2-dev xterm gyp
  ```

then download the needed stuff:

  ```
  $ mkdir yocto
  $ cd yocto/

  $ git clone http://git.yoctoproject.org/git/poky
  $ git clone http://github.com/crosswalk-project/meta-crosswalk
  $ git clone http://github.com/otcshare/meta-crosswalk-embedded
  ```

now go to poky and checkout the 'dizzy' branch, which is where the new things
are and then jump to its build environment:

  ```
  $ cd poky/
  $ git checkout dizzy
  $ source oe-init-build-env

  ```

You had no conf/local.conf file so a configuration file has therefore been
created for you with some default values, but we need still to add the
meta-crosswalk and meta-crosswalk-embedded layers in conf/bblayers.conf - mind to change
the lines below with the **full path of the directory you are cloning the
repos** (in our case it was /media/yocto/) :

  ```
BBLAYERS ?= " \
  /media/yocto/poky/meta \
  /media/yocto/poky/meta-yocto \
  /media/yocto/poky/meta-yocto-bsp \
  /media/yocto/meta-crosswalk-embedded \
  /media/yocto/meta-crosswalk \
  "
  ```

In the conf/local.conf file, set the distro variable from the default (usually "poky")
to "crosswalk-embedded", which stands for our custom Yocto distribution:

  ```
DISTRO ?= "crosswalk-embedded"
  ```

then, set in conf/local.conf the genericx86-64 machine (you could try a
different architecture but we haven't yet):

  ```
MACHINE ?= "genericx86-64"
  ```

besides, still in local.conf we need to pick our simple Web UI to boot the
system into. So add this in the same file:

  ```
IMAGE_INSTALL_append = " ventus"
  ```

Now close the file and let's cook the image: 
  ```
  $ bitbake core-image-crosswalk-embedded
  ```  

It will take several hours to download much of the dependencies, build and
etc. Relax now. If everything goes fine, you will have the following directory
with the images built in inside:
  ```
  $ ls tmp/deploy/images/genericx86-64/*.hddimg
  $ tmp/deploy/images/genericx86-64/core-image-crosswalk-embedded-genericx86-64-20141009113028.hddimg
  $ tmp/deploy/images/genericx86-64/core-image-crosswalk-embedded-genericx86-64.hddimg
  ```

Make sure you have now inserted a USB flash drive, **checking the correct file
descriptor** that Linux will be using with the `sudo fdisk -l` command. For
example in our system it is ```/dev/sdd```, so the following is what we used to
flash it:
  ```
  $ cd tmp/deploy/images/genericx86-64/
  $ sudo dd if=core-image-crosswalk-embedded-genericx86-64.hddimg of=/dev/sdd
  $ sync 
  $ sudo eject /dev/sdd
  ```

You are able now to boot the flash drive in your hardware and play around with
a couple of examples such as HexGL, Emberwind, WebRTC, WebGL motion detector
(using Web cam), Crosswalk, among others. Note that what you are seeing there
as a "desktop" is actually the HTML5 & CSS3 window manager
[Ventus](http://www.rlamana.es/ventus/), so don't get confused because nothing
you see there is native but Web-based ;)

# Bugs

What's not working:

  * Sound
  * Touchpad

# Contributing

Start discussions by opening issues in github (right menu) and after agreed
the necessary changes with the developers, send a pull request.

# License

Embedded Crosswalk Project (ECP) uses the BSD license (check the LICENSE file in the project).
