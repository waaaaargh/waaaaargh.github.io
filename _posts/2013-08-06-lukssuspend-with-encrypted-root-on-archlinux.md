---
layout: post
title: "luksSuspend with encrypted root on Archlinux"
description: "Want to make "
category: "GNU&Linux"
tags: ["archlinux", "linux", "crypto"]
---
{% include JB/setup %}

Okay. You have your full-disk encryption all set up and ready to rock. But, 
of course, this takes away a lot of practicality. You can't just do
`systemctl suspend` any more and just leave, because then your key is in the
ram which might be a problem if you have periphery busses that allow DMA,
such as PCMCIA, Express Card, even most docking station interfaces. Besides,
would you trust `xscreensaver` or whatever it is you're using with all of your
sensitive Data? I sure as hell wouldn't.

So, you have to shut down your laptop everytime you want to change locations
and reopen all those programs again when you're at your destination. Not 
exactly what I call user-friendly.

As it turns out, `luks` already has a feature for that, `luksSuspend` and 
`luksResume`, to be precise. On systems with encrypted root partition this
however is not usefull at all since after the `luksSuspend` you don't have 
access to the `cryptsetup` binary any more, because this one is on the
root partition we just suspended.

But hold on, there's a way around that!

**Warning:** The following is a extremely dirty hack and should be tried
in a safe testing environment, such as a virtual machine. I am not liable for
any data loss you might suffer.

1. Make a folder in your (unencrypted) boot partition or on some other
   unencrypted storage. Call it something like `suspend_hack`, the name
   doesn't matter anyway.

2. In that folder create the folders `/dev`, `/proc`, `/run/`, `/sys` and 
   `/usr`.

3. In the just-created `/usr` folder, make the folders `bin` and `lib`.

4. Copy the binaries for `bash`, `sync` and `cryptsetup` 
   to `/boot/suspend_hack/usr/bin`. You can find out which binaries those
   programs have with

    <pre>
        which programname
    </pre>

5. For each of those find out which `.so` files they depend on and copy
   them to the appropriate path under `/boot/suspend_hack`. You might
   have to create some directories yourself. To find out which binaries
   a program needs, you can just do

    <pre>
        ldd `which programname`
    </pre>

6. We're nearly there! all we have to do now is prepare our `chroot`
   script. mine looks something like this:

    <pre>
    #!/usr/bin/bash

    CHROOT_TARGET=/boot/resume
    BIND_PATHS="/sys /proc /dev"

    ## prepare chroot
    for p in ${BIND_PATHS}; do
        mount -o bind ${p} ${CHROOT_TARGET}${p}
    done 

    openvt -c 8 -sw -- chroot ${CHROOT_TARGET} /suspend.sh

    sleep 1

    ## unmound chroot bindings
    for p in ${BIND_PATHS}; do
        umount ${CHROOT_TARGET}${p}
    done
    </pre>

7. Then we have of course need to add another shellscript that actually
   does the work (mine is under `/boot/resume/suspend.sh`). Change 
   `/dev/mapper/main` to your root LVM device.

    <pre>
    #!/usr/bin/bash
    sync
    cryptsetup luksSuspend /dev/mapper/main
    sync

    echo mem > /sys/power/state

    cryptsetup luksResume /dev/mapper/main
    </pre>

and that's it! Just call the first script created from outside your chroot
and it should switch to the 8th virtual terminal and suspend. When your box
wakes up again, it should ask for your passphrase and switch back to the 
graphical user interface.
