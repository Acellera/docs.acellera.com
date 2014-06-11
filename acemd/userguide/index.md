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

# Introduction


ACEMD is a high performance molecular dynamics code for biomolecular systems designed specifically for NVIDIA GPUs. Simple and fast,  ACEMD uses very similar commands and input files of NAMD [NAMD] and output files as NAMD or Gromacs. The three design target features of ACEMD are:

ACEMD is fast, as it was designed from scratch to be an optimized MD engine that exploits the computational power of graphical processing units (GPUs). ACEMD provides the equivalent performance of parallel CPU simulations using many tens to hundreds of processors, depending on the number and type of GPU cards installed. ACEMD has the following features and capabilities:

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
* Metadynamics using PLUMED 1.3, including parallel tempering metadynamics

## Fundamental units

* Length Ångström (0.1nm)
* Energy kcal/mol
* Temperature Kelvin
* Mass g/mol

These yield the derived units:

* time unit  t = 48.88821 femtoseconds$
* velocity unit v = Ångström/t
* Boltzmann constant  kB = 0.001987191 kcal/mol/K

## Force field parameters

The default force-field formats used by ACEMD are CHARMM including cross-term support and Amber force-fields. The code can also simulate OPLS force-fields in CHARMM format via translated force-fields. To build the molecular system it is possible to use any tool that it suitable for these force fields. For instance, charmm or VMD (free) for the Charmm forcefield and ambertools (free) for Amber.

### CHARMM force field

The topology and parameters force fields for CHARMM are available at http://mackerell.umaryland.edu/CHARMM_ff_params.html. Please refer to this link for the complete and latest information. Different forcefields might give diffent results for your system. Also the quality of the forcefields have greatly improved in the last years. Use always the latest forcefield unless you have a specific reason not to. For Charmm we suggest two forcefields:

* __CHARMM36__ - this is the last available forcefield for Charmm. It is probably the best forcefield for membrane proteins. The free online website CHARMM-GUI is the best to set up a membrane system also changing the type of lipids. Combined parameter file which uses the new lipid naming scheme used by CHARMM-GUI (cfr e.g. P1 instead of previously-used P for the phosporus atom in lipids). 
* __CHARMM22*__ - this is a revised version of the Charmm forcefield derived by DE SHAW research. It is older than Charmm36 but it works better for protein folding. You can also mix Charmm22* for protein with Charmm36 for proteins. Note that this is different from Charmm22 without star which should never be used. Charmm27 also widely used is known to overstabilize helices and should also be avoided now that there are better alternatives.

### AMBER force field

If you are used to AMBER input files, then you can just keep doing that. It is necessary to provide a PDB instead of the CRD file (load it and save it from VMD). Use the following commands in your ACEMD configuration files :

    coordinates  your_pdb_file
    amber on
    parmfile your_topology_file
    1-4scaling 0.83333
    
We suggest that you keep switching when using AMBER force fields in ACEMD to avoid force discontinuities at the cutoff distance as you do for CHARMM. Special parameters for the Amber forcefields can be found at http://www.pharmacy.manchester.ac.uk/bryce/amber

To generate a system with tleap using ff13SB and new ions, load the following settings

    source leaprc.ff13SB # FF13 forcefield 
    source leaprc.gaff   # If custom ligands/lipids: GAFF forcefield
    # Modified ions and ion names Joung/Cheatham ion parameters for TIP3P water
    loadamberparams frcmod.ionsjc_tip3p
    loadoff ions08.lib

### OPLS force field

The use of OPLS force field parameters is possible using the version in CHARMM format, but only for the protein portion. See http://brooks.scripps.edu/charmm_docs/Data/oplsaa-toppar.tgz. This requires to use the configuration parameter "vdwgeometricsigma on". This forcefield is less used and so less tested, in particular we have not tested the porting 

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


    $ ./acemd.release.strip.3156 --command langevintemp
    langevintemp <+ve float>   [0.]    The set point in K for the Langevin thermostat


## Selecting a GPU

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

## Running parallel ensembles

ACEMD Pro supports ensemble simulations for replica exchange molecular dynamics. Ensemble mode is automatically enabled if ACEMD is run via MPI. For example, to run an 8 replica ensemble:

```
$ mpirun -np 8 acemd input
```

Note that this assumes that the MPI environment is appropriately configured. In this mode no explicit -device flag should be used. ACEMD will run one replica per GPU and assume that all GPUs on the allocated hosts are available for its use.

## Simulation Configuration

ACEMD simulations are configured using a single input file. This file is parsed as a TCL script, so can include programmatic elements. The syntax of the input script is very similar to that of NAMD. The script is read in its entirety before the simulation commences. If commands are duplicated, generally only the last setting will be used. For example:

    structure struct1.pdb
    structure struct2.pdb
    run 100
    run 1000

configures ACEMD to use the structure file struct2.pdb and to run for 1000 iterations.

## Quick Configurations

A complete specification for an ACEMD simulation requires configuration of input and output files, force field parameters and thermodynamic ensemble. Explicitly writing the full configuration can result in a long input file. ACEMD includes a set of pre-defined parameter sets for common simulation configurations. These are activated using the protocol command. For example:

    protocol run/NVT
    protocol ff/Amber

configures ACEMD to simulate in the isothermal ensemble and to expect Amber force field input files. Unlike most other commands, protocol is executed as soon as it is encountered and can be specified multiple times.

If ACEMD is run with the flag -verbose then as each protocol is executed, the commands that it specifies are printed out in the log file. These can be captured for use in an explicit input file. Any inappropriate settings can be overriden by re-issuing the command afterwards. For example:

    protocol run/NVT
    protocol run/Amber
    parmfile amber.prmtop
    run      10ns

changes the default setting for the name of the Amber parameter file and the length of the simulation.

The following protocols are available:

* __Run types__
  * run/NVT run in the isotermal ensemble, using a Langevin thermostat set at 300 K
  * run/NPT run in the isothermal-isobaric ensemble, using a Langevin thermostat at 300.K and a Berendsen barostat at 1atm.
  * run/NVE run in the microcanonical ensemble.
  * run/CG run a coarse-grained simulation.
* __Force field types__
  * ff/Amber configure for Amber force fields
  * ff/CHARMM27 configure for CHARMM version 22 and 27 force fields
  * ff/CHARMM36 configure for CHARMM version 36 force fields
  * ff/Martini configure for Martini force field
  * ff/OPLS configure for OPLS force field
  
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

# Full Configuration

Shown below is an example of an explicit input file for an Amber force field simulation in the NPT ensemble using positional restraints:

    # Inputs
       	coordinates         structure.pdb
       	bincoordinates      input.coor
       	binvelocities       input.vel
       	extendedsystem      input.xsc
    # Configure force calculation
    	pme                 on
    	pmegridspacing      1.0
    	pmefreq             2
    	cutoff              9.
    	switching           on
    	switchdist          7.5
    # Configure integration
    	rigidbonds          all
    	hydrogenscale       4.0
    	timestep            4.0
    # Configure output
    	energyfreq          5000
    	xtcfreq             25000
    	xtcfile             trajectory.xtc
    	outputname          output
    	restart             on
    	restartfreq         25000
    	restartname         restart      
    # Configure thermostat
    	langevin            on
    	langevintemp        300.0
    	langevindamping     0.1
    # Configure barostat 
    	berendsenpressure                on
    	berendsenpressuretarget          1.01325
    	berendsenpressurerelaxationtime  800
    	useflexiblecell     off
    	useconstantratio    on
    # Configure positional restraints
    	constraints         on
    	consref             structure.pdb
    	constraintscaling   1.0
    # Configure for Amber ff
    	amber       on
    	parmfile    structure.prmtop 
    	switching   on
    	switchdist  7.5
    	exclude     scaled1-4
    	1-4scaling  0.8333333333333333
    # run
    	run                 100ns
    	
## Input Files

ACEMD expects input coordinates in PDB or Bincoor format, specified using the commands coordinates and bincoordinates respectively. An initial velocity field may also be supplied using velocities or binvelocities.

The dimensions of the unit cell may also be specified in a file given by extendedsystem. If present, this will over-ride any celldimensions setting.

For simulations using CHARMM format models, a topolgy file in PSF format must be specified with structure along with force field parameters by parameters.

For Amber simulations, the combined topology/force field PRMTOP file is required, specified with the command parmfile.

## Output Files

ACEMD can produce trajectories in both DCD and XTC formats. XTC trajectories are compressed, so save on disk space, but may not be read by all analysis programs.

At the end of a simulation, ACEMD also outputs the final system state (coordinates and velocities) in NAMD Bincoord format. The filename prefix of these files is by defualt output and can be overriden with the command outputname.

If the barostat is enabled, the unit cell dimensions are emitted into the output file suffix .xstfile whenever the energies are printed.

## Standard output (stdout)

During a run ACEMD will print a summary of the system energies to stdout. This should sually be saved in a log file for future reference using re-direction, for example:

```
$ acemd --device 1 input > log.txt
```

During ensemble runs ech replica's log file is automatically redirected to a file called log.N, where N is the 0-based replica index.

All log lines not containing an energy are prefixed with # to facilitate greping. Attention should be paid to the log for lines prefixed # WARNING. These will generally indicate when a default value for a parameter has been used, indicating that a configuration option may have been unintentionally omitted.

The log also contains a measure of the current performance of the simulation, expressed in ns/day, along with an estimate of the completion time. GPU temperatures and fan speeds are also printed, for monitoring purposes.

An example is shown below:


    #     Step	      Bond	     Angle	     Dihed	      Elec	       VDW	        PE	        KE	      External	     Total	      Temp	      Pres	   PresAve
             0	   74.8328	  340.6115	  750.4010	-72143.0851	 4123.4423	-66853.7976	14371.4091	        0.0000	-52482.3885	  298.9427	16519.2258	16519.2258
           100	  486.7075	 1343.1273	 1024.4554	-78224.9388	 6361.2833	-69009.3652	15448.9447	        0.0000	-53560.4206	  321.3568	  -35.9722	 -250.7526
    # Simulation rate 19.32 (ave) 19.32 (inst) ns/day. Estimated completion Wed Aug 28 15:53:34 2013
    # NVML : 0 : Fan 31%	 Temp 43C	 Mem Used 253MB Free 769MB Total 1023MB

## Restarting

ACEMD can perform checkpointing to allow an aborted simulation to be resumed, using the commands restart, restartname and restartfreq. Frequency of restart dump should generally be set to match the trajectory output frequency.

Restart coordinate and velocity files are in NAMD Bincoord format.

Simulations started using protocols will be configured to restart by default.

# Extra

## Apply periodic boundary conditions

ACEMD by default does not wrap in the output the coordinates around the periodic box, so when the molecular system is visualized water molecules appear to diffuse away. This is done such that you can measure diffusion coefficients. It is just a visualization choice. In the newest versions of ACEMD there is the command "wrap all" which instead wraps the coordinates of every atoms.
Best and most flexible way to do the wrapping is after the simulation with VMD. From VMD 1.8.7 (http://www.ks.uiuc.edu/Research/vmd/plugins/pbctools/) you can wrap using the information in the DCD unitcell (load the dcd directly on a structure file rather than the pdb):

```
pbc wrap -center bb -centersel protein -compound res
```

NB: In the case that you want to align the protein as well to some structure, be careful to align only after wrapping, otherwise the wrapping is wrong as the periodic box is rotated by the alignment. With the older version of VMD, 1.8.6 use instead: pbc wrap -sel "not protein" -center "protein" -all

# Citations

When publishing results with ACEMD please cite:

* M. Harvey, G. Giupponi and G. De Fabritiis, ACEMD: Accelerated molecular dynamics simulations in the microseconds          timescale, J. Chem. Theory and Comput. 5, 1632 (2009).

Additonally, please read and consider citing the following methods papers:

* M. J. Harvey and G. De Fabritiis, An implementation of the smooth particle-mesh Ewald (PME) method on GPU hardware, J. Chem. Theory Comput., 5, 2371–2377 (2009)
* U. Essmann, L. Perera, M. L. Berkowitz, T. Darden, H. Lee and L. G. Pedersen, A smooth Particle Mesh Ewald Method, J. Chem. Phys. 103, 8577 (1995)
* Mass repatitioning (dt=4fs) Feenstra, K. A., Hess, B., Berendsen, H. J. C., Improving efficiency of large time-scale molecular dynamics simulations of hydrogen-rich systems, J. Comp. Chem. 20, 786(1999).
* M-SHAKE V. Krautler, W. F. Van Gunsteren, P. H. Hunenberger, A fast SHAKE algorithm to solve distance constraint equations for small molecules in molecular dynamics simulations, J. Comp. Chem. 22, 501 (2001).
* RATTLE H. C. Andersen, Rattle: A velocity version of the shake algorithm for molecular dynamics calculations, J. Comp. Phys. 52, 24 (1983).
