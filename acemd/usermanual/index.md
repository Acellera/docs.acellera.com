---
title: ACEMD User Manual
layout: default
name: User Manual
description:
---

Contents
========

1. auto-gen TOC:
{:toc}

# Introduction


ACEMD is a high performance molecular dynamics code for biomolecular systems designed specifically for NVIDIA GPUs. Simple and fast,  ACEMD uses very similar commands and input files of NAMD and output files as NAMD or Gromacs. The three design target features of ACEMD are:

ACEMD is fast, as it was designed from scratch to be an optimized MD engine that exploits the computational power of graphical processing units (GPUs). ACEMD provides the equivalent performance of parallel CPU simulations using many tens to hundreds of processors, depending on the number and type of GPU cards installed. ACEMD has the following features and capabilities:

* Force fields Amber, CHARMM, OPLS and Martini
* Input file formats PDB, PSF, PRMTOP, NAMD Bincoor
* Output file formats PDB, NAMD Bincoor, DCD, XTC
* Electrostatics PME, integrator Velocity Verlet, long timesteps with H mass repartitioning, unit cell cuboid, fully periodic
* Thermostat Langevin, barostat Berendsen
* Constraints and restraints M-SHAKE with RATTLE correction, positional harmonic restraints
* Scripting TCL and C interfaces
* Metadynamics using PLUMED 1.3/2.0, including parallel tempering metadynamics and replica exchange

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

The default force-field formats used by ACEMD are CHARMM including cross-term support and Amber force-fields. The code can also simulate OPLS force-fields in CHARMM format via translated force-fields. To build the molecular system it is possible to use any tool that it suitable for these force fields. For instance, charmm or 
<a href="http://www.ks.uiuc.edu/Research/vmd/" target="_parent"> VMD </a> for the Charmm forcefield and ambertools (free) for Amber.

### CHARMM force field

ACEMD reads directly the psf and parameter files for Charmm. You can generate these files as usual with the Charmm suite or with VMD. The topology and parameters force fields for CHARMM are available at <a href="http://mackerell.umaryland.edu/CHARMM_ff_params.html" target="_parent">Mackerell's page</a>. Please refer to this link for the complete and latest information. Different forcefields might give diffent results for your system. Also the quality of the forcefields have greatly improved in the last years. Use always the latest forcefield unless you have a specific reason not to. For Charmm we suggest two forcefields:

* __CHARMM36__ - this is the latest available forcefield for Charmm. It is probably the best forcefield for membrane proteins. The free (for academics) online website <a href="http://www.charmm-gui.org" target="_parent">CHARMM-GUI</a> is very good to set up a membrane system also changing the type of lipids.  However it is still possible to the same using VMD, just a bit more scripting is required.
* __CHARMM22star__ - this is a revised version of the Charmm forcefield derived by DE SHAW research. It is older than Charmm36 but it works better for protein folding. You can also mix Charmm22star for protein with Charmm36 for proteins. Note that this is different from Charmm22 without star which should never be used. Charmm27 also widely used is known to overstabilize helices and should also be avoided now that there are better alternatives. Charmm22star files are available from us here: [charmm22star topology file](http://docs.acellera.com/acemd/pub/top_all22star_prot.inp) and [parameter file](http://docs.acellera.com/acemd/pub/par_all22star_prot.inp). There is no official distribution of charmm22star files, but these were manually validated against Desmond implementation and we are sufficiently confident that they are correct and acemd produces the correct forces.

### AMBER force field

AMBER input files are read by ACEMD, so  it is only necessary to provide a PDB instead of the CRD file (load it and save it from VMD). Use the following commands in your ACEMD configuration files :

    coordinates  your_pdb_file
    amber on
    parmfile your_topology_file
    1-4scaling 0.83333
    
We suggest that you keep switching when using AMBER force fields in ACEMD to avoid force discontinuities at the cutoff distance as you do for CHARMM. 

The latest forcefield from Amber is the best to use

* __Amber14SB__ solves several shortcomings of previous Amber forcefields. Special parameters for the Amber forcefields can be found at <a href="http://www.pharmacy.manchester.ac.uk/bryce/amber" target="_parent"> this site</a>. To obtain the latest forcefield simply download the latest version of AmberTools for free. 
 
To generate a system with tleap using ff14SB and new ions, load the following settings

    source leaprc.ff14SB # FF14 forcefield 
    source leaprc.gaff   # If custom ligands: GAFF forcefield
    # Modified ions and ion names Joung/Cheatham ion parameters for TIP3P water
    loadamberparams frcmod.ionsjc_tip3p
    loadoff ions08.lib

### OPLS force field

The use of OPLS force field parameters is possible using the version in [CHARMM format](http://brooks.scripps.edu/charmm_docs/Data/oplsaa-toppar.tgz), but only for the protein portion. This requires the use of the configuration parameter "vdwgeometricsigma on". This forcefield is less used and so less tested, in particular we have not tested the porting 

# Running ACEMD

ACEMD is a command line program, invoked with the command ACEMD. If ACEMD is not found, or reports any other error at startup, please refer to the [ACEMD Installation Guide](../install).

## Specifying an Input File

When run without any arguments ACEMD will attempt to read a configuration from the file input and run a simulation on the first GPU in the system. If the input file does not exist ACEMD will print out a help message and exit. 
An alternative input file can be specified by putting the filename on the command line, for example:

```
$ acemd equilibrate.conf
```

The input file contains all of the commands required to configure and run a simulation. The specification for the system to be simulated resides in separate files, eeg PDB, Bincoor files for coordinates, PSF, PRMTOP files for topology and force field parameters.

## Command Help

ACEMD contains a built-in reference manaual for all configuration commands, which can be access from the command line and online at [ACEMD command reference](../commands). To all commands that pertain to a particular topic, use the command:

```
$ acemd --command [section]
```

where section is one of the topics listed by the command acemd -command alone. For help on a particular command, use:

```
$ acemd --command [command]
```

This will give a description of the command's function along with any required arguments and default value. For example:


    $ acemd --command langevintemp
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

# Simulation Configuration

ACEMD simulations are configured using a single input file. This file is parsed as a TCL script, so can include programmatic elements. The syntax of the input script is very similar to that of NAMD. The script is read in its entirety before the simulation commences. If commands are duplicated, generally only the last setting will be used. For example:

    structure struct1.pdb
    structure struct2.pdb
    run 100
    run 1000

configures ACEMD to use the structure file struct2.pdb and to run for 1000 iterations.


## NVT vs NPT ensemble

The Langevin thermostat is needed to keep the system in the NVT ensemble. This is the suggested ensemble for production runs. The langevindamping should be as small as possible in order to thermalize the system without affecting the transport parameters (diffusion). We suggest to use langevindamping 0.1 for all production runs in NVT. A langevindamping 1 is better during equilibration.

ACEMD implements a Berendsen barostat designed for the equilibration of molecular systems (globular and in a membrane) to then start NVT production runs. With the system sizes which are achievable nowadays it is not necessary to have a pressure control in the production run, unless you really know what you are doing (for large number of atoms all ensembles are equivalent statistically). For molecular systems up to 100,000 atoms in a membrane allow for an equilibration of 20 ns, for globular proteins 1 to 5 ns are sufficient.


## Full Configuration Example

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

This is equivalent to simple write in the quick format:

    protocol ff/amber
    protocol run/NPT
    langevintemp        300.0
    constraints         on
    run 100ns
    
## Input Files

ACEMD expects input coordinates in PDB or Bincoor format, specified using the commands coordinates and bincoordinates respectively. An initial velocity field may also be supplied using velocities or binvelocities.
The dimensions of the unit cell may also be specified in a file given by extendedsystem. If present, this will over-ride any celldimensions setting.

For simulations using CHARMM format models, a topolgy file in PSF format must be specified with structure along with force field parameters by parameters.
For Amber simulations, the combined topology/force field PRMTOP file is required, specified with the command parmfile.

## Output Files

ACEMD can produce trajectories in both DCD and XTC formats. XTC trajectories are compressed, so save on disk space, but may not be read by all analysis programs.

At the end of a simulation, ACEMD also outputs the final system state (coordinates and velocities) in NAMD Bincoord format. The filename prefix of these files is by defualt output and can be overriden with the command outputname.

If the barostat is enabled, the unit cell dimensions are emitted into the output file suffix .xstfile whenever the energies are printed.

## Standard output

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

ACEMD can perform checkpointing to allow an aborted simulation to be resumed, using the commands restart, restartname and restartfreq. Frequency of restart dump should generally be set to match the trajectory output frequency. Checkpointing and restarting is very powerful in ACEMD and seamless. Trajectory files are automatically appended upon a restart. 

Restart coordinate and velocity files are in NAMD Bincoord format. Simulations started using protocols will be configured to restart by default.

# Advanced material

##  Tcl Scripting

The entire input file is seen by ACEMD as a Tcl script. You can interleave Tcl command with the commands shown in the [command reference manual](../commands). Tcl is also useful to manipulate the molecular systems by reading coordinates, velocities and forces on-the-fly while the simulation is running. Tcl scripts are executed in the CPU, so they can be expensive if the number of atoms involved is large (depending on system size, but target for less than 100 atoms if possible). For simple harmonic positional constraints use the constraints command instead.

ACEMD calls two tcl functions, **calc_forces_init** at startup and **calc_forces** at every step. Note that calc_forces is processed on the CPU.

Harmonic positional constraints can be applied on selected atoms. This is useful during equilibration but also in production runs, so ACEMD implements it in the fastest possible way: directly computed on the GPU. There are no limitations on the number of atoms to which the constraints are applied. Similar and more flexible constraints can also be applied using Tcl scripting.

For instance the following Tcl scripting example applies a flat bottom potential restrain to a single atom group in 1D

    #Normal acemd conf file
    protocol run/NVT
    protocol ff/Amber
    run 1000
    # TCL
    set structure mystructure.pdb
    set Krestrain 10
    set axis {1 0 0}
    set logfreq 1000
    tclforces on
    #
    proc restrain1_1D_flatbottom { _coor O group Dir K d log } {
       # USAGE: restrains 1 group of atoms with a flat bottom potential 
       #                    at a distance $d from $O in the direction $Dir with constant $K  
       upvar $_coor coor
       global logfreq
       #
       set dr [expr [vecdot $coor($group) $Dir] - [vecdot $O $Dir] ]
       set DR  [expr abs($dr) - $d]
       if {$DR > 0} {
         if {$dr > 0} {
           set DR [expr $dr - $d]
         } else {
           set DR [expr $dr + $d]
         }
       set f [vecscale [expr -$K*$DR] $Dir]
       addforce $group $f
       set step [ getstep ]
       if {$step % $logfreq == 0} {
         set E [ expr 0.5*$K*$DR*$DR ]
         print "$log $step $dr $DR $E"
       }
       }
    }
    #
    proc calcforces_init {} {
    global structure coor1 glist1 gcom1
    #
    # Extraction of atoms and coordinates from pdb file.
    readpdb pdb $structure
    #
    # Loading of atoms with beta column equal to '1' and storage of
    # selected atoms index values and coordinates.
    loadsystem pdb 1 id1 coor1    
    #
    # Creation of group corresponding to the center of mass of
    # selected atoms to which we will apply the restrain.
    set gcom1 [ addgroup $id1 ]
    #
    # Creation of a list of groups for every selected atom.
    # We need this to obtain later the reference position for the center of mass.
    get_groups id1 glist1
    #
    }
    #
    proc calcforces {} {
    global coor1 glist1 gcom1 axis Krestrain logfreq
    #
    ## Loading of system's current coordinates
    loadcoords coords
    #
    ## Getting atom selection coordinates
    # Current
    set gcom1_pos $coords($gcom1)
    # Reference
    set gcom1_start [ center_of_mass glist1 coor1 ]
    #    
    ## Applying a 1D restrain to the center of mass of the atom selection
    ## using as reference coordinates those extracted from the pdb.
    restrain1_1D_flatbottom $gcom1_start $gcom1 $axis $Krestrain 0 "log_text"
    #
    return;
    }

## Debug TCL scripts

The following definitions can be prepended **before** the TCL script in order to enable light debugging (function calls).

    rename proc _proc
    _proc proc {name arglist body} {
        _proc $name $arglist [concat "proc_start;" $body ";proc_end"]
    }
    _proc proc_start {} {
        puts stderr ">>> ENTER PROC [lindex [info level -1] 0]"
        for {set level [expr [info level] -1]} {$level > 0} {incr level -1} {
            puts stderr "  LEVEL $level: [info level $level]"
        }
        puts stderr ""
    }
    _proc proc_end {} {
        puts stderr ">>> LEAVE PROC [lindex [info level -1] 0]\n"
    }

The following functions can be added **after** the calcforces definition to enable very verbose (complete trace) debugging.


    proc tracer { a b } { puts "TRACE $b: $a" }
    trace add execution calcforces enterstep tracer


## Plugin interface

ACEMD is easily extended by adding plugin modules written in C and dynamically loaded by the application. Current plugins include metadynamics, a power biased free energy calculation method. It is easy to think of ways on which users might want to customize ACEMD for their needs. In practice, the plugin interface give access to the position,velocities and forces at each iteration from a C interface. Please visit the plugin web page for more information.

## Getting support

Users can receive individual and confidential support at Acellera Ltd via **support@acellera.com**.
There is also a [ACEMD user forum](http://www.acellera.com/support/community). 

## Apply periodic boundary conditions

ACEMD by default does not wrap in the output the coordinates around the periodic box, so when the molecular system is visualized water molecules appear to diffuse away. This is done such that you can measure diffusion coefficients. It is just a visualization choice. In the newest versions of ACEMD there is the command "wrap all" which instead wraps the coordinates of every atoms.
Best and most flexible way to do the wrapping is after the simulation with VMD. From VMD 1.8.7 (http://www.ks.uiuc.edu/Research/vmd/plugins/pbctools/) you can wrap using the information in the DCD/xtc unitcell (load the trajectory directly on a structure file rather than the pdb):

```
pbc wrap -center bb -centersel protein -compound res -all
```

In the case that you want to align the protein as well to some structure, __be careful to align only after wrapping__, otherwise the wrapping is wrong as the periodic box is rotated by the alignment. 

# Citations

When publishing results with ACEMD please cite:

* M. Harvey, G. Giupponi and G. De Fabritiis, ACEMD: Accelerated molecular dynamics simulations in the microseconds          timescale, J. Chem. Theory and Comput. 5, 1632 (2009).

Additonally, please read and consider citing the following methods papers:

* M. J. Harvey and G. De Fabritiis, An implementation of the smooth particle-mesh Ewald (PME) method on GPU hardware, J. Chem. Theory Comput., 5, 2371–2377 (2009)
* U. Essmann, L. Perera, M. L. Berkowitz, T. Darden, H. Lee and L. G. Pedersen, A smooth Particle Mesh Ewald Method, J. Chem. Phys. 103, 8577 (1995)
* Mass repatitioning (dt=4fs) Feenstra, K. A., Hess, B., Berendsen, H. J. C., Improving efficiency of large time-scale molecular dynamics simulations of hydrogen-rich systems, J. Comp. Chem. 20, 786(1999).
* M-SHAKE V. Krautler, W. F. Van Gunsteren, P. H. Hunenberger, A fast SHAKE algorithm to solve distance constraint equations for small molecules in molecular dynamics simulations, J. Comp. Chem. 22, 501 (2001).
* RATTLE H. C. Andersen, Rattle: A velocity version of the shake algorithm for molecular dynamics calculations, J. Comp. Phys. 52, 24 (1983).
