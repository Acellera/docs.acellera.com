---
title: ACEMD User Guide
layout: default
name: User Guide
description: ACEMD user guide
---


1 auto-gen TOC:
{:toc}

ACEMD
=====

ACEMD is a high performance molecular dynamics simulator for biomolecular systems designed for NVIDIA GPUs. ACEMD has the following features and capabilities:

* Force fields Amber, CHARMM, OPLS and Martini
* Input file formats PDB, PSF, PRMTOP, NAMD Bincoor
* Output file formats PDB, NAMD Bincoor, DCD, XTC
* Electrostatics PME with 4th order splines
* Thermostat Langevin
* Barostat Berendsen
* Constraints and restraints M-SHAKE with RATTLE correction 
* Positional restraints
* Integrator Velocity Verlet, long timesteps with H mass repartitioning
* Unit Cell Cuboid, fully periodic
* Scripting TCL and C interfaces
* Metadynamics using PLUMED 1.3, including parallel tempering

Fundamental units
-----------------

* Length Ångström (0.1nm)
* Energy kcal/mol
* Temperature Kelvin
* Mass g/mol

These yield the derived units:

* time unit  t = 48.88821 femtoseconds$
* velocity unit v = Ångström/t
* Boltzmann constant  kB = 0.001987191 kcal/mol/K

# Running ACEMD

ACEMD is a command line program, invoked with the command ACEMD. If ACEMD is not found, or reports any other error at startup, please refer to the ACEMD Installation Guide

## Specifying an Input File

When run without any arguments ACEMD will attempt to read a configuration from the file input and run a simulation on the first GPU in the system. If the input file does not exist ACEMD will print out a help message and exit.

An alternative input file can be specified by putting the filename on the command line, for example:

```
$ acemd input-equilibrate
```

The input file contains all of the commands required to configure and run a simulation. The specification for the system to be simulated resides in separate files, eeg PDB, Bincoor files for coordinates, PSF, PRMTOP files for topology and force field parameters.
