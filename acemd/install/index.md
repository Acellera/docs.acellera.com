---
title: What title?
layout: default
name: A name
description: A longer description
---


It is generally not recommend updating the driver unless there is a necessary bug fix in a later version.
That, said, here is what you should do assuming the OS is CentOS:

* As root, "yum update". Reboot if the kernel is updated.
* Download the new driver from http://us.download.nvidia.com/XFree86/Linux-x86_64/331.79/NVIDIA-Linux-x86_64-331.79.run
* chmod +x NVIDIA-Linux-x86_64-331.79.run
* As root, telinit 3 to drop the text mode
* Run NVIDIA installer, answering yes to all questions
* Reboot


This is a header
================

This is a sub-header
--------------------

This is _markdown_.
