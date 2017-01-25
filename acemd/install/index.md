---
title: ACEMD Install guide
layout: default
name: ACEMD Install guide
description: 
---

# Prerequistites

* __Hardware__ ACEMD requires a modern NVIDIA GPU: we recommend the Geforce 980, 1080 or Tesla K and P-series GPUs. The latest NVIDIA Driver should be installed, available from [here](http://www.nvidia.com/Download/index.aspx)

* __Software__ ACEMD requires a 64bit Linux OS.  ACEMD is formally qualified with Red Hat EL6, but will work with any Linux distribution supported by NVIDIA CUDA, provided the GLIBC version is 2.12 or higher. Note that RHEL 5 and SLES 10 are not supported.  For replica exchange support, please ensure that the OS's `openmpi` packages are isntalled.

# Installing ACEMD

ACEMD is now distributed as part of Acellera's High Throughput Molecular Dynamics Toolkit. To install this, please follow the instructions [here](https://www.htmd.org/academic-download.html)

* __Existing customers__ We recommend that you change to the above method too.

# ACEMD Basic

Even if you have not purchased an ACEMD Pro license, ACEMD can be used in Basic mode, in which it is restricted to running on the first GPU of your computer.

Basic mode first requires registration and acceptance of our EULA. to do this, please run the command `htmd_register` and enter your details.

ACEMD may then be run from the command line with the command `acemd`, or via HTMD

# ACEMD Pro

If you have purchases an ACEMD Pro node-locked license from Acellera you will be issued with an activation token. After following the installation instructions above, issue the command:
```
 $ activate_license [activation-token]
```
This will download and install your license key. If any error is reported please contact Acellera.

ACEMD may then be run from the command line with the command `acemd`, or via HTMD

Customers purchasing group/floating license will receive additional installation  download details for Acellera's license server along with their order confirmation. The installation guide for the license server is below.


If you have obtained ACEMD Pro as part of a Metrocubo solution, you will receive the machine with ACEMD installed, configured and ready to run.



# More on Replica Exchange and MPI

ACEMD supports ensemble simulations for replica exchange molecular dynamics. This is implemented using MPI for parallel communication. Because there are many different MPI implementations, all with different binary interfaces, ACEMD includes a wrapper library to allow it be used with whichever MPI your system is configured with.

If you do not intend to use ensemble methods, and receive no warnings or errors when running ACEMD, you do not need to take any further action.

The wrapper library, libmpiwrapper.so, provided with ACEMD is built against OpenMPI and compatible with Red Hat / Centos 6+  OS packages. If you wish to use a different MPI, you must recompile the wrapper as follows:
```
    $ cd [HTMD installation home]/mpiwrapper
    $ make
```   
If you receive any errors, check that the MPI development packages and C compiler are installed. If successful, the libmpiwrapper.so in the lib directory will be reconfigured for your system.

# License Server

Floating licenses allow ACEMD to be used on any machine, subject to a limit on the number of concurrent instances. Floating licenses require a license server to be running. This should be installed on a machine that all clients can connect to over the network. In a cluster environment, for example, it should be run on an administrative machine.

The following instructions assume that you have administrative/root access to the machine in question, that the Operating System is Red Hat EL6, and that you wish to configure the license server to run automatically as a system service.

first download the license server [here](https://download.acellera.com/acemd-release-2016/acellera-license-server.tgz).

To install the license server, first create a new userid for it, for example:
```
    # useradd sglmd
```
Next, copy the script `$ACEMD_HOME/sglmd/etc/lmgrd.sglmd` to `/etc/init.d`. Edit the copy to include the correct value for `ACEMD_HOME` for your system. We recommend using ```/opt/acellera```

Add the license server as a system process and enable it at runlevel 3 and 5:
```
    # chkconfig --add lmgrd.sglmd
    # chkconfig --level 35 lmgrd.sglmd on
```
Install the license file as `license.dat` in `ACEMD_HOME`. Ensure that its permissions allow it to be read under the sglmd user context.

Start the license server using:
```
    # service lmgrd.sglmd start
```

The license server will produce a log file in /var/log/lmgrd.sglmd. Check this file to confirm that all is working:

```
    $ head -2 /var/log/lmgrd.sglmd
    2013/01/01 00:00:00 Server starting on host.example.com, port 27000, max components 64, max connections 256
    Enabling acemd 1.0 60 28-apr-2014 
    Ready...
```

Check that the license components available matchthose purchased (in this example 60 acemd tokens with expiry date 28 April 2014).

Finally, ensure that the environment variable `SG_LICENSE_FILE` is set with the connection details of the license server. This has the format port@hostname. The default port number is 27000. For example:

```
    $ export SG_LICENSE_FILE=27000@host.example.com
```

If you encounter any problems installing the license server, please contact us directly at support@acellera.com

# Nvidia driver update procedure

It is generally not recommend updating the driver unless there is a necessary bug fix in a later version.
That, said, here is what you should do assuming the OS is CentOS:


* As root, "yum update". Reboot if the kernel is updated.
* Download the new driver from [here](http://www.nvidia.com/Download/index.aspx) 
* `chmod +x NVIDIA-Linux-x86_64-XXX.XX.run`
* As root, `telinit 3` to drop the text mode
* Run NVIDIA installer, answering yes to all questions
* `reboot`

