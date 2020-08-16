---
title: "Installing DOS 6.22 from MSDN"
date: 2019-05-18T12:04:35-08:00
tags: ["DOS","VirtualBox"]
---
## Install MS DOS 6.22 from MSDN into VirtualBox VM

If you have a Visual Studio subscription, formerly known as a MSDN subscription, you may have noticed that MS DOS is available as a download. The following is instructions on how you can install the provided MS DOS 6.22 download into a new [Virtual Box](https://virtualbox.org) VM.

## Steps

### Download and extract DOS 6.22
Download the MS DOS 6.22 download from your [Visual Studio subscription](https://my.visualstudio.com/Downloads?q=dos&pgroup=).

![](/images/DOS622_0.png#thumbnail)

You end up with a .EXE file. I used [7-Zip](https://7-zip.org) to extract the contents. 

![](/images/DOS622_1.png#thumbnail)

The 3 files we're interested in are the ones with filenames starting with "144" in the DISKS subfolder.
These are images of the 3 1.44 MB floppy disks originally used to install this version of MS DOS.
Copy these out to a safe location.

### Create and start a new VirtualBox VM

Launch VirtualBox and start the process to create a new VM.

Select DOS as the OS, you can leave the other settings at their defaults.
![](/images/DOS622_2.png#thumbnail)

"Insert" the first floppy disk image into the virtual floppy drive, start or reset the VM as nescesary, and you'll reboot into the MS DOS setup menu.

![](/images/DOS622_3.png#thumbnail)

Unfortunately if you now hit enter to begin the install, you'll get an error message starting that this is an upgrade only version of MS DOS that requires a version of MS DOS be already installed.

![](/images/DOS622_5.png#thumbnail)

This is actually quite easy to work around. Instead of hitting enter to start setup, hit F3, F3 to exit to a command prompt window.

You should end up at A:\ prompt. Type fdisk (enter)

    A:\> fdisk

![](/images/DOS622_4.png#thumbnail)

Select option 1: **Create DOS Partition...**.

Then, select option 1: **Set active partition**.

Answer **Y** to the question asking for confirmation on using max size and setting the partition active and let it reboot.

Once it reboots, you will have to hit F3, F3 again to get back to the command prompt

Format the c drive with the /s (system) flag. This will initialize the c drive and copy enough operating system files to make the DOS 6.22 setup happy to proceed when we launch it again later.

    A:\> format c: /s

![](/images/DOS622_6.png)

Then launch the setup program again. The /g argument is to prevent it trying to create an uninstall disk.

    A:\> setup /g

![](/images/DOS622_7.png)

Work your way through the setup prompts keeping the default values. Setup will stop and prompt you to insert disk 2, and then disk 3 as it progresses. At these points, change the attached floppy image and hit enter to continue. 

![](/images/DOS622_9.png)

Finally, you will be prompted to remove all disks - detach the last image and reboot into DOS 6.22!

![](/images/DOS622_10.png)