---
layout: post
title: "Compiling Vim with Python3 support on Debian"
description: "The way to +python3 on Debian"
category: gnu&linux
---

Okay. So you want to write python3 code in vim and use some plugins that use
python3? Well, you got to roll your own.

Get the sources
---------------

Make a new directory and chdir into it:
```
mkdir build
cd build
```

Get the source code from the debian repositories
```
sudo apt-get source vim-gtk
```

Get the build dependencies
--------------------------
```
sudo apt-get build-dep vim-gtk
```

```
sudo apt-get install python3.3-dev devscripts build-essential
```

Modify the build script
-----------------------

Go to the `vim-7.x.xxx` subfolder and edit the file `debian/rules`.

find the string `--disable-python3interp` and change it into 
`--enable-python3interp=dyn`.

Build debian packages
---------------------
```
debuild -us -uc
```

This is going to take a while, so grab a coffee or a beer or something.

Installing the packages
-----------------------
`debuild` puts the packages directly into the parent folder.

```
cd ..
sudo dpkg -i vim-gnome*
```
