---
layout: post
title: Ubuntu 16.04 LTS Workstation upgrade
tags: sysadmin, ubuntu, 16.04, lts, upgrade, radeon, video, driver, xorg
---

Usually I don't jump on the bandwagon, but I can change my mind and today I wanted to be on the bleeding edge of the ubuntu wagon. Went on and upgraded my ubuntu 14.04 LTS to 16.04 LTS and the upgrade was not flawless like I have come to expect from debian. 

For some reason the normal do-release-upgrade did not pick up a new release, so I hade to use the command `do-release-upgrade -d` to get the beta. 

### Mount by UUID 

My partition layout for all machines (workstation and servers) is always the same. 

{% highlight Text only %}
/dev/sda1 /boot 
/dev/mapper/vg0-sysroot /
/dev/mapper/vg0-sysuser /usr
/dev/mapper/vg0-syshome /home
/dev/mapper/vg0-sysvar  /var
/dev/mapper/vg0-swap    swap 
{% endhighlight% %}

And then my fstab usually look something like this.

{% highlight Text only %}
UUID="da877b81-58f9-493a-b152-d5a61e0210c6" / ext4 defaults 0 1
UUID="b055a78d-2642-4835-9dbc-6828872b93d9" /usr ext4 default 0 2
{% endhighlight %}

When I booted my fresh 16.04 for some reason /usr was impossible to mount by UUID, all else / /home etc, works perfectly. But not /usr. 

Solution: 

In initramfs I mounted /usr under /root/usr to get access to an editor, then remounted /root rw from ro to be able to edit my fstab. Commands below. 

{% highlight Text Only %}
lvm vgchange -ay 
mount -o rw,remount /root
mount /dev/mapper/vg0-sysusr /root/usr
/root/usr/bin/vim.tiny /root/fstab 
{% endhighlight %}

Then I replaced the UUID=... entry with /dev/mapper/vg0-sysusr 

# Video driver in Xorg

My next hurdle that was a big one, was to transition from fglrx to amdgpu that is the new standard in ubuntu 16.04. fglrx does not even support the xorg 1.8 ABI that is default now. 

This took a long long time, and lots of googling and to end up in the wrong solution path. 

What I ended up doing was. 

### Disable hi res console for grub and boot, so it never leaves textmode. 

This is something I personally prefer since those consoles tend to be much faster, and monitors find the correct mode quicker and such. 

This is done by adding this to your /etc/default/grub and issuing update-grub2 
This is added with the other options on `GRUB_CMDLINE_LINUX_DEFAULT`

{% highlight Text Only %}
nofb vga=normal video=vesafb:off
{% endhighlight %}

### nomodeset

For a long time nomodeset has been default on the grub cmdline, I am not sure why. But setting nomodeset or nomodeset=1 or radeon.nomodeset=1 will make your amdgpu unable to load. 

`*ERROR* vgacon disables radeon kernel modesetting`

This will show up in your dmesg/kernel log

So my entire grub cmd line ended up like this: 
`GRUB_CMDLINE_LINUX_DEFAULT="quiet splash nofb vga=normal video=vesafb:off"`

# Screen configuration in X  

My workstation has three screens, two are in portrait mode and one in landscape mode. 

Quick and dirty solution is making a xrandr script that will rotate my screens, and position them correctly for me. 

{% highlight Bash %}
#/bin/bash

xrandr --output HDMI-2 --rotate left
xrandr --output DVI-0 --rotate left
xrandr --output DVI-0 --right-of HDMI-2
xrandr --output DVI-1 --right-of DVI-0
xrandr --output DVI-1 --pos +2160x770
{% endhighlight %}

### Hardware 

* i5-4670K CPU @ 3.40GHz
* 16G Ram 
* SSD 
* Radeon R7 370

### Conclusion 

It was not super easy to figure out the nomodeset. But I am super happy with my upgrade. And my desktop feels very very fast now. Scrolling massive webpages is fluent, opening new tabs. Using libreoffice felt very responsive and fast. I am actually suprised by this desktop experience. 

My window manager of choice is i3 wm, when I found it I will never go to anything else I think. It is wonderful. 

