---
title: ACEMD Install guide
layout: default
name: ACEMD Install guide
description: 
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

# Install ACEMD Basic

ACEMD Basic is freely available to academic and industrial users for single-machine, single-GPU use. ACEMD Basic can be obtained by making a request on the [Acellera website](http://www.acellera.com/products/acemd/getacemd/)

To install ACEMD Basic follow the instructions for ACEMD PRO.

## FAQ

* I registered but didn't receive any email. You should receive a confirmation email shortly after registering. If you have not received any email within 10 minutes, please check your spam folder and double-check the email address you entered. ACEMD Basic applications are approved individually, during EU office hours, so there may be a delay before you receive download approval

* My request for ACEMD Basic was refused. ACEMD Basic is available only to legitimate academic and industrial users. You provided insufficient or incorrect information in your request to allow us to confirm your identity. Please re-register, being sure to give full contact details and an institutional/business email address.

* When I try to run `acemd', I get the error `bash: acemd: command not found'. Check that the directory containing the acemd binary is included in the PATH environment variable

* When I try to run `acemd', I get the error `acemd: error while loading shared libraries: libcufft.so.4:'
Check that the directory containing the missing library is included in the LD_LIBRARY_PATH environment variable. libcudafft.so.4 and libcudart.so.4 are included in the bin directory of the acemd distribution.


# More on MPI

ACEMD supports ensemble simulations for replica exchange molecular dynamics. This is implemented using MPI for parallel communication. Because there are many different MPI implementations, all with different binary interfaces, ACEMD includes a wrapper library to allow it be used with whichever MPI your system is configured with.

If you do not intend to use ensemble methods, and receive no warnings or errors when running ACEMD, you do not need to take any further action.

The wrapper library, libmpiwrapper.so, provided with ACEMD is built against OpenMPI 1.5.4. If you wish to use a different MPI, you must recompile the wrapper as follows:

    $ cd $ACEMD_HOME/mpiwrapper
    $ make
    
If you receive any errors, check that the MPI development packages and C compiler are installed. If successful, the libmpiwrapper.so in the lib directory will be reconfigured for your system.

# License Server

Floating licenses allow ACEMD Pro to be used on any machine, subject to a limit on the number of concurrent instances. Floating licenses require a license server to be running. This should be installed on a machine that all clients can connect to over the network. In a cluster environment, for example, it should be run on an administrative machine.

The following instructions assume that you have administrative/root access to the machine in question, that the Operating System is Red Hat EL6, and that you wish to configure the license server to run automatically as a system service.

To install the license server, first create a new userid for it, for example:

    # useradd sglmd

Next, copy the script ACEMD_HOME/sglmd/etc/lmgrd.sglmd to /etc/init.d. Edit the copy to include the correct value for ACEMD_HOME for your system.

Add the license server as a system process and enable it at runlevel 3 and 5:

    # chkconfig --add lmgrd.sglmd
    # chkconfig --level 35 lmgrd.sglmd on

Install the license file as license.dat in ACEMD_HOME. Ensure that its permissions allow it to be read under the sglmd user context.

Start the license server using:

    # service lmgrd.sglmd start

The license server will produce a log file in /var/log/lmgrd.sglmd. Check this file to confirm that all is working:

    $ head -2 /var/log/lmgrd.sglmd
    2013/01/01 00:00:00 Server starting on host.example.com, port 27000, max components 64, max connections 256
    Enabling acemd 1.0 60 28-apr-2014 
    Ready...

Check that the license components available matchthose purchased (in this example 60 acemd tokens with expiry date 28 April 2014).

Finally, ensure that the environment variable SG_LICENSE_FILE is set with the connection details of the license server. This has the format port@hostname. The detaul port number is 27000. For example:

    $ export SG_LICENSE_FILE=27000@host.example.com

If you encounter any problems installing the license server, please contact us directly at support@acellera.com

# Nvidia driver update procedure

It is generally not recommend updating the driver unless there is a necessary bug fix in a later version.
That, said, here is what you should do assuming the OS is CentOS:

* As root, "yum update". Reboot if the kernel is updated.
* Download the new driver from http://us.download.nvidia.com/XFree86/Linux-x86_64/331.79/NVIDIA-Linux-x86_64-331.79.run
* chmod +x NVIDIA-Linux-x86_64-331.79.run
* As root, telinit 3 to drop the text mode
* Run NVIDIA installer, answering yes to all questions
* Reboot

