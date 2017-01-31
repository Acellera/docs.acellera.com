---
title: ACEMD 3 Beta Documentation
layout: default
name: ACEMD 3 Beta Documentation
description: ACEMD 3 Beta Documentation
---

# Introduction

Welcome to the ACEMD 3 Beta programme! 

ACEMD 3 is a substantial 
New features:
 * Improved performance
 * CPU support
 * Simplied input file

# Installing ACEMD 3 Beta

ACEMD is now distributed as part of Acellera's High Throughput Molecular Dynamics Toolkit. To install this, please follow the instructions [here](https://www.htmd.org/academic-download.html). 

Once you have HTMD installed, install the ACEMD 3 Beta with the command
```
$ conda install acemd3 -y
```

# Running ACEMD 3 Beta

ACEMD 3 Beta is run with the command ```acemd3```. If you do not have a valid ACEMD Pro license the programme will run in Basic mode, with operation limited to the first GPU on your system.

# Input file changes

The ACEMD input file has been simplified. Where commands have been deprecated or changed name ACEMD will print a warning explaining the change. The full command set is documented [here](/acemd3/commands.md) 

TCL scripting is no longer supported in the input file. If you used TCL for applying restraints please read the documentation on [applying restraints](/acemd3/restraints.md)

# Discontinued Features

ACEMD 3 Beta does not contain support for:
 * Ensemble execution
 * Replica exchange
 * ACEMD Plugins
 * PLUMED 1

# Support

If you encounter any issues with ACEMD 3 Beta please open an issue on our [GitHub issue tracker](https://github.com/acellera/acemd_issues/issues)