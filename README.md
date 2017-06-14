# RootUnion
---------

I stumbled across some software that I wanted to test on my Desktop running Linux, but it required a great deal of packages to be installed. I had just formated the machine and everything was perfectly setup. I did not want to mess it up by installing a lot of packages that I would later have to clean up, so I thought that I would setup a temp aufs or overlayfs on top of file system, that way it would automatically be cleaned up by a simple restart. But setting this up on top of the root file system is not that easy. You need to mount everything outside of the directories that you wish to lay over, which is difficult when `/` is as far as it goes. The only way to get even further is though the ramdisk. I did find a few solutions that was able to do this, but they did not meet the requirement or was to old, to poorly documented and to messy in the code to waste any time on.

## Features

RootUnion supports `aufs` and `overlay` incl. the older `overlayfs` module. You can setup any device that could be setup via `fstab`, that means choosing `Label`, `UUID`, `dev` path etc., and selecting `type` and `options`. The options part is mostly what did not meet my requirements on the solutions that I found. Without it, you can't properly use subvolumes on `btrfs` file systems. It also support `tmpfs` setup if you don't want a persistent overlay.

It also has an optional `/boot` option that will create a `tmpfs` overlay on `/boot`. This could be done via `fstab`, since this partition is mounted from `init`, but it makes sense to have a quick option for this to go with the `tmpfs` root overlay. If you don't want a persistent root overlay, you properly don't want any changes to be made to the `/boot` partition either.

## CryptSetup

dm-crypt already packs scripts to handle this during boot and these are run before this script. It makes little sense to add support for cryptsetup.

## What is this good for?

3 examples of what this script can be useful for.

1. You do a lot of software testing that requires you to constantly install a lot of different packages, most of which should be removed ones you are done testing. Using this script, you can boot a `tmpfs` overlay. After all of your testing, simply reboot and your machine is back to normal.

2. You spend a lot of time making system backup just in case you mess something up and need to restore it. Using this script you could format your root partition using `btrfs`, create two subvolumes, setup your OS on one and then add the other as an overlay. If you should ever mess anything up, simply clear the overlay subvolume and your OS is fully restored.

3. Your wife/girlfriend needs to borrow your computer and has a tendency to break things .....

## Install

Copy the content of `src/` to the respective directories and re-generate your ramdisk. In ubuntu or ubuntu-derived distro's you do this with the command `update-initramfs -u`.

The script is disabled by default, it need to be enabled in the config file or using kernel boot args. Checkout the config file `etc/rootunion.conf` for more information on the setup options.
