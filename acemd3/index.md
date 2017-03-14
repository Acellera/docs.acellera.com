---
title: ACEMD 3 Beta Documentation
layout: default
name: ACEMD 3 Beta Documentation
description: ACEMD 3 Beta Documentation
---

# Welcome to the ACEMD 3 Beta programme

ACEMD 3 is a substantial development over ACEMD 2. Its new features include:

* Improved performance
* CPU support
* Simplified input file


These major changes mean we will be running an extended private Beta programme before making a general release. Thank you for agreeing to participate!

# Installing ACEMD 3 Beta

ACEMD is now distributed as part of Acellera's High Throughput Molecular Dynamics (HTMD) Toolkit. To install this, please follow the instructions [here](https://www.htmd.org/academic-download.html). 


Once you have HTMD installed, install the ACEMD 3 Beta with the command


```
$ conda install acemd3 -y
```

# Running ACEMD 3 Beta

ACEMD 3 Beta is run with the command ```acemd3```. If you do not have a valid ACEMD Pro license the programme will run in Basic mode, with operation limited to the first GPU on your system.

If you have an ACEMD Pro license you may run on multiple GPUs with the ```--device```` flag, specifying the indices of the GPUs to  use as an argument.
To run in CPU only mode, use the argument ```--platform CPU```. ACEMD will attempt to use all available CPU cores unless the environment variable NCPUS is set.

# Input file changes

The ACEMD input file has been simplified. Where commands have been deprecated or changed name ACEMD will print a warning explaining the change. The full command set is documented [here](/acemd3/commands.html). 


TCL scripting is no longer supported in the input file. If you used TCL for applying restraints please read the documentation on [applying restraints](/acemd3/restraints.html).

# Discontinued Features

ACEMD 3 Beta does not contain support for:

* Ensemble execution
* Replica exchange
* ACEMD Plugins
* PLUMED 1

# Support

If you encounter any issues with ACEMD 3 Beta please open an issue on our [GitHub issue tracker](https://github.com/acellera/acemd_issues/issues).
