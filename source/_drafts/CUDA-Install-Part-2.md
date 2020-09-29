---
title: CUDA_Install_Part_2
tags:
  - cuda
  - nvidia
  - gpu
  - driver
categories:
  - lab
date: 2015-04-27 00:00:00
---

先ずCUDAを入れたのでnouveauのドライバをブロックするのは大事，
<!-- more -->
{% vimhl bash %}
1. Create a file at
/etc/modprobe.d/blacklist-nouveau.conf with the following contents:
2. Regenerate the kernel initramfs:
$ sudo dracut --force
{% endvimhl %}

Applications that use the NVIDIA driver, such as a CUDA application or the X server (if any), will normally automatically create these files if they are missing using the setuid nvidia-modprobe tool that is bundled with the NVIDIA Driver. However, some systems disallow setuid binaries, so if these files do not exist, you can create them manually either by running the command nvidia-smi as root at boot time or by using a startup script such as the one below:

{% vimhl bash %}
#!/bin/bash
/sbin/modprobe nvidia
if [ "$?" -eq 0 ]; then
  # Count the number of NVIDIA controllers found.
  NVDEVS=`lspci | grep -i NVIDIA`
  N3D=`echo "$NVDEVS" | grep "3D controller" | wc -l`
  NVGA=`echo "$NVDEVS" | grep "VGA compatible controller" | wc -l`
  N=`expr $N3D + $NVGA - 1`
  for i in `seq 0 $N`; do
    mknod -m 666 /dev/nvidia$i c 195 $i
  done
  mknod -m 666 /dev/nvidiactl c 195 255
else fiexit 1
/sbin/modprobe nvidia-uvm
if [ "$?" -eq 0 ]; then
  # Find out the major device number used by the nvidia-uvm driver
  D=`grep nvidia-uvm /proc/devices | awk '{print $1}'`
  mknod -m 666 /dev/nvidia-uvm c $D 0
else
fi
{% endvimhl %}

Check that the device files/dev/nvidia* exist and have the correct (0666) file permissions.



最も関心な部分はオンラインバージョンのCUDA Toolkit Documentation には実は書いてある



To make sure X doesn't use a certain GPU for display, you need to specify which other GPU to use for display. This is done by editing the xorg.conf file located at /etc/xorg/xorg.conf.

You will need to add a section that resembles the following to your xorg.conf file:

{% vimhl bash %}
#!bash
Section "Device"
    Identifier    "Device0"
    Driver        "driver_name"
    VendorName    "vendor_name"
    BusID         "bus_id"
EndSection
{% endvimhl %}

The exact details of what you will need to add differ on a case-by-case basis. For example, if you have two NVIDIA GPUs and you want the first GPU to be used for display, you would replace "driver_name" with "nvidia", "vendor_name" with "NVIDIA Corporation" and "bus_id" with the Bus ID of the GPU.
The Bus ID will resemble "PCI:00:02.0" and can be found by running lspci.
