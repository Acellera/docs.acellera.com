---
title: ACEMD User Guide
layout: default
name: User Guide
description: ACEMD user guide
---

Contents
========

1. auto-gen TOC:
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
$ acemd equilibrate.conf
```

The input file contains all of the commands required to configure and run a simulation. The specification for the system to be simulated resides in separate files, eeg PDB, Bincoor files for coordinates, PSF, PRMTOP files for topology and force field parameters.

## Command Help

ACEMD contains a built-in reference manaual for all configuration commands, which can be access from the command line. To all commands that pertain to a particular topic, use the command:

```
$ acemd --command [section]
```

where section is one of the topics listed by the command acemd -command alone. For help on a particular command, use:

```
$ acemd --command [command]
```

This will give a description of the command's function along with any required arguments and default value. For example:

```
$ ./acemd.release.strip.3156 --command langevintemp
langevintemp <+ve float>   [0.]    The set point in K for the Langevin thermostat
```

# Selecting a GPU

ACEMD Basic will run only on the first GPU in the system. The following section applies only to ACEMD Pro users.

ACEMD will by default try to run on the first GPU available in the system. If several instance of ACEMD are launched they will normally1 all run on the first GPU, leaving any other devices idle. To explicity set the GPU to run on, use the command-line flag -device:

```
$ acemd --device 2
```

If the device specified does not exist, ACEMD will automatically select an available GPU.

If several GPUs are given as a comma-separated list to -device, ACEMD will attempt to run a single simulation in parallel accross them. For example:

```
$ acemd --device 0,1,2
```

When running in parallel note that performance may not always improve as more GPUs are added. 

# Running parallel ensembles

ACEMD Pro supports ensemble simulations for replica exchange molecular dynamics. Ensemble mode is automatically enabled if ACEMD is run via MPI. For example, to run an 8 replica ensemble:
```
$ mpirun -np 8 acemd input
```

Note that this assumes that the MPI environment is appropriately configured. In this mode no explicit -device flag should be used. ACEMD will run one replica per GPU and assume that all GPUs on the allocated hosts are available for its use.

## Simulation Configuration

ACEMD simulations are configured using a single input file. This file is parsed as a TCL script, so can include programmatic elements. The syntax of the input script is very similar to that of NAMD. The script is read in its entirety before the simulation commences. If commands are duplicated, generally only the last setting will be used. For example:

```
structure struct1.pdb
structure struct2.pdb
run 100
run 1000
```

configures ACEMD to use the structure file struct2.pdb and to run for 1000 iterations.

# Quick Configurations

A complete specification for an ACEMD simulation requires configuration of input and output files, force field parameters and thermodynamic ensemble. Explicitly writing the full configuration can result in a long input file. ACEMD includes a set of pre-defined parameter sets for common simulation configurations. These are activated using the protocol command. For example:

```
protocol run/NVT
protocol ff/Amber
```

configures ACEMD to simulate in the isothermal ensemble and to expect Amber force field input files. Unlike most other commands, protocol is executed as soon as it is encountered and can be specified multiple times.

If ACEMD is run with the flag -verbose then as each protocol is executed, the commands that it specifies are printed out in the log file. These can be captured for use in an explicit input file. Any inappropriate settings can be overriden by re-issuing the command afterwards. For example:

```
protocol run/NVT
protocol run/Amber

parmfile amber.prmtop
run      10ns
```

changes the default setting for the name of the Amber parameter file and the length of the simulation.

The following protocols are available:

* Run types
 * run/NVT run in the isotermal ensemble, using a Langevin thermostat set at 300.K
 * run/NPT run in the isothermal-isobaric ensemble, using a Langevin thermostat at 300.K and a Berendsen barostat at 1atm.
 * run/NVE run in the microcanonical ensemble.
 * run/CG run a coarse-grained simulation.
* Force field types
 * ff/Amber configure for Amber force fields
 * ff/CHARMM27 configure for CHARMM version 22 and 27 force fields
 * ff/CHARMM36 configure for CHARMM version 36 force fields
 * ff/Martini configure for Martini force field
 * ff/OPLS configure for OPLS force field
 * 
These protocols assume the following file naming conventions:

* Input
 * Coordinates: structure.pdb
 * CHARMM Topology: structure.psf
 * CHARMM Parameters: parameters
 * Amber Parameters: structure.prmtop
 * Extended System: input.xsc
* Output
 * Trajectory: trajectory.xtc
 * Final state: output.coor output.vel output.xsc
