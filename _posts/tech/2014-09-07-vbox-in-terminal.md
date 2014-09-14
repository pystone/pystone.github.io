---
layout: post
title: "VirtualBox in terminal on osx"
category: tech
keyword: VirtualBox, Linux, terminal, OS X Mavericks
description: "We all have the experience that install a Linux in VM on osx. But every time we want to do something in Linux, we have to open a VM window. Even if we install a Linux without GUI, a VM window with command-line interface is also necessary. Why not simply turn on the virtual Linux on Terminal in OS X and connect to it with ssh? Here I'll tell you the way to do so."
---

## Preview

First I will show you the idea to run and control a virtual Linux directly on Terminal in OS X.

<a href="/images/vbox_in_terminal/p0.png" target="_blank">
<img id="image-preview" class="reduced" src="/images/vbox_in_terminal/p0.png" alt>
</a>

## Necessary files to download

1. VirtualBox 4.3.14: <http://download.virtualbox.org/virtualbox/4.3.14/VirtualBox-4.3.14-95030-OSX.dmg>

2. VirtualBox 4.3.14 Oracle VM VirtualBox Extension Pack: <http://download.virtualbox.org/virtualbox/4.3.14/Oracle_VM_VirtualBox_Extension_Pack-4.3.14-95030.vbox-extpack>

3. Install image of any Linux distribution without GUI. This tutorial will use CentOS 6.5 64-bit as an example. Some commands given below shall subject to adaptation (such as `yum`).

4. VBoxGuestAdditions.iso: <http://download.virtualbox.org/virtualbox/> (Find the right version)

## Install VirtualBox, Linux and Extension Pack

Installing VirtualBox and Linux is an easy thing for you, I will save the time here. After that, you should install VirtualBox Extension in OS X. Download and double-click it and you are done.

## Configure ssh settings

To connect to the Linux using ssh, we should install ssh in Linux first. Turn on the virtual machine in VirtualBox in normal way, log in and type:

    sudo yum install openssh-server openssh-clients

Now you can shut down the virtual machine:

    sudo shutdown -h now

In the settings of Linux virtual machine, you should make the network attached to *NAT*.

<img src="/images/vbox_in_terminal/p1.png">

Next we should bind the virtual machine in a specific port so that we can ssh to it. Open terminal in osx and type:

    
    VBoxManage setextradata "Cent_6.5" "VBoxInternal/Devices/e1000/0/LUN#0/Config/ssh/Protocol" TCP
    VBoxManage setextradata "Cent_6.5" "VBoxInternal/Devices/e1000/0/LUN#0/Config/ssh/GuestPort" 22
    VBoxManage setextradata "Cent_6.5" "VBoxInternal/Devices/e1000/0/LUN#0/Config/ssh/HostPort" 3456

In the third command, 3456 is binding port of the host machine, indicating when ssh to localhost:3456, you are connecting to virtual machine. "Cent_6.5" is the name of the Linux virtual machine. I will use the same syntax in the following passage.


## Manage the virtual machine in Terminal

Once you are done with all the stuffs above, you can manage your virtual machine in Terminal in osx. Type the command to turn on the vm:

    VBoxHeadless -startvm "Cent_6.5” &

Connet to it with:

    ssh USERNAME@localhost -p 3456

As memtioned above, 3456 is the port you set just now. USERNAME is the user name of you Linux account.

You can shut down the Linux in Terminal in osx using:

    VBoxManage controlvm “Cent_6.5” poweroff

Or using the following command in Linux has the same effect:

    shutdown -h now

### Create alias 

You may not want to type all these things every time. So just create an alias in your .bashrc or .zshrc(yeah, I'm using zsh~). For example, here are my alias:

    alias startvm="VBoxHeadless --startvm 'Cent_6.5' &"
    alias stopvm="VBoxManage controlvm 'Cent_6.5' poweroff"
    alias sshtovm="ssh py@localhost -p 3456"

By doing so, you can turn on, connect to and shut down vm by typing:

    startvm
    sshtovm
    stopvm

By now, you can manage your virtual machine simply in Terminal. But there is one thing left, file sharing between virtual machine and host.

## File sharing between virtual machine and host

Now your virtual Linux and your host OS X are two separated systems. If you are not terrified with transfering files using scp, you can close this blog now. But if you want to access files in OS X in your Linux or vice versa, you may want to go on.

### Virtual machine preparations

To enable the sharing feature, you should do some preparations in Linux.

For Debian or Debian based distributions (Ubuntu)

    apt-get install dkms build-essential linux-headers-generic

For Mandirva

    urpmi dkms gcc make libgomp1 glibc-devel kernel-devel kernel-headers

For Fedora

    yum install dkms binutils gcc make patch libgomp glibc-headers glibc-devel kernel-headers kernel-devel

or

    yum install dkms binutils gcc make patch libgomp glibc-headers glibc-devel kernel-headers kernel-pae-devel


### Install VBoxGuestAdditions

Download the VBoxGuestAdditions.iso and mount it to Linux in settings.

<img src="/images/vbox_in_terminal/p2.png">

In the virtual machine, type the following commands to mount iso file and install:

    sudo makedir /media/cdrom    
    sudo mount /dev/sr0 /media/cdrom
    cd /media/cdrom
    sudo sh ./VBoxLinuxAdditions.run

One thing to be noted is that if no Xserver installed, you might get the error "Could not find the X.Org or XFree86 Window System, skipping.”. Just ignore it.

### Select folder to be shared


In the vm settings, switch to Shared Folders tab. Add the folders you want to share. 

<img src="/images/vbox_in_terminal/p2.png">

Keep the name of it (take ‘PY’ for example) in mind, and we’ll use it later. Type the following command in Terminal in vm: (PY is the name you set in the vm settings)

    mkdir ~/sf
    sudo mount -t vboxsf PY ~/sf

~/sf is the shared folder. One more thing, every time you restart the vm, the shared folder is unmounted and you should mount it again. So you can save the mount command in a .sh script and run it when start.

Now, it's a perfect integrated vm in your osx. Just enjoy it!

## Reference

1. <https://forums.virtualbox.org/viewtopic.php?t=15679>
2. <http://www.andrew-kirkpatrick.com/2011/12/virtualbox-guest-additions-with-shared-folders-on-mac-os-x/>
3. <https://forums.virtualbox.org/viewtopic.php?t=15868>





