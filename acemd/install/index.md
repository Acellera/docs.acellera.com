---
title: What title?
layout: default
name: A name
description: A longer description
---

# Prerequisites

Before installing ACEMD, please ensure that your computer satisfies the following requirements:

* __Hardware__. One or more x86 Processor. For best performance we recommend Intel E3 or E5 CPUs (Sandy Bridge or later), with Intel X79 or C20x chipset.
One or more NVIDIA GPUs. ACEMD is formally qualified with Tesla 20x0, K20, K10 and Geforce GTX 680 and Titan, but will work with other Fermi (GF10x, GF110) and Kepler (GK10x, GK110) models. Older Tesla 10x0 and Geforce 2x0 (G80, G92) GPUs are also supported but with reduced performance and functionality. For best performance, GPUs should be attached to the same PCI root complex.

* __Software__. 64bit Linux OS. ACEMD is formally qualified with Red Hat EL6, but will work with any Linux distribution supported by NVIDIA CUDA, provided the GLIBC version is 2.12 or higher. Note that RHEL 5 and SLES 10 are not supported.
NVIDIA Driver version 310.44 or higher. For Geforce GTX 780 and Titan, the minimum driver version is 325.15.
CUDA Toolkit version 4.2. ACEMD Basic is supplied with CUDA libraries for Red Hat EL6. If the system uses an alternative OS, please download and install the appropriate version of CUDA 4.2 from https://developer.nvidia.com/cuda-toolkit-42-archive.

# Installing ACEMD Pro

ACEMD Pro is available directly from Acellera. Please contact info@acellera.com for more information.  If you have obtained ACEMD Pro as part of a Metrocubo solution, you will receive the machine with ACEMD installed, configured and ready to run.

To install ACEMD Pro first decide on a suitable installation location, eg /opt/acemd, and set the environment variable ACEMD_HOME to point there. To simplify running ACEMD, you may also wish to add the directory ACEMD_HOME/bin to the PATH environment variable. You must ensure that the environment variable LD_LIBRARY_PATH is set to include the directory containing the CUDA runtime libraries. For example:

    $ export ACEMD_HOME=/opt/acemd
    $ export PATH=$PATH:$ACEMD_HOME/bin
    $ export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/path/to/usr/local/cuda/lib64
    $ export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:$ACEMD_HOME/lib

You might want to add the line above in .bashrc to be set everytime. Next uncompress the distribution archive in that directory:

    $ mkdir -p $ACEMD_HOME
    $ cd $ACEMD_HOME
    $ tar -zxvf /path/to/acemd_pro_XXXX.tgz

If you intend to run replica exchange molecular dynamics please read section 4.3 before continuing. Finally, if using host-based licensing install the license file as license.dat in the directory ACEMD_HOME. If using a license server, follow the instructions in Section 5.

ACEMD can now be run with the command acemd.

## FAQ

* When I try to run `acemd', I get the error `acemd: error while loading shared libraries: ...' libcuda.so.1
The NVIDIA Kernel driver is not installed correctly.

* libcudafft.so.4, libcudart.so.4. These are from the CUDA runtime. Ensure that CUDA Toolkit 4.2 is installed and its library directory included in LD_LIBRARY_PATH.

* libmpiwrapper.so is part of the ACEMD distribution. Ensure that ACEMD_HOME/lib is inclued in LD_LIBRARY_PATH.

* libmpi, libopen-rte, libopen-pal. Check that your Operating System's OpenMPI runtime packages are installed2 and the library directory included, or read section 4.3

# Nvidia driver update procedure

It is generally not recommend updating the driver unless there is a necessary bug fix in a later version.
That, said, here is what you should do assuming the OS is CentOS:

* As root, "yum update". Reboot if the kernel is updated.
* Download the new driver from http://us.download.nvidia.com/XFree86/Linux-x86_64/331.79/NVIDIA-Linux-x86_64-331.79.run
* chmod +x NVIDIA-Linux-x86_64-331.79.run
* As root, telinit 3 to drop the text mode
* Run NVIDIA installer, answering yes to all questions
* Reboot

