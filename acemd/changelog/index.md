---
page.title: ACEMD ChangeLog
layout: default
name: Changelog
description: Description of feature changes in any revision of ACEMD
---


Release acemd 3212 – Oct 2013
-----------------------------

**Main changes:**

- Support for Geforce GTX 780 and Titan GPUs
- Support for Parallel tempering metadynamics with PLUMED 1.3
- Expanded Plugin interface and new manual — see doc/ACEMD-Extension-Guide.pdf
- Built-in command reference, see “–command”
- ACECloud integration for remote execution of ACEMD, see “–cloud” and doc/ACEMD-AceCloud-User-Guide.pdf
- “Protocols” to simplify input files — see doc/ACEMD-User-Guide.pdf
- Support for Martini Force field (see “cosangles” and “martiniswitching”)
- Linear repulsive potential to prevent aggregation in multi-ligand simulations (see “repulsionadd” etc)
- New license server option for floating license seats


**Minor enhancements:**

- Improved parallel performance
- Parallel runs no longer require GPUs to share a PCIe bus
- Output wrapping to the unit cell with “wrap all” no longer splits molecules over the periodic boundary
- Improved performance for NPT simulations
- Support for PDB/PSF files produced by CHARMM-GUI
- Report total charge of system if non-zero
- Many TCL vector operations now built in

Release acemd 2728 – Oct 2012
-----------------------------

**Major release require CUDA4.2 Nvidia drivers. Main changes:**

- New parallel protocols via mpirun and parallel execution via, e.g. acemd –device 1,2,3
- Faster serial and parallel execution up of 30%
- Requires CUDA4.2 drivers, so driver update might be necessary
- Up to 20% faster for small systems on a single GPU. (500ns/day on L-Iduonic acid, 115ns/day on DHFR)
- Support for Kepler GPUs
- New plugin interface and plugins, PLUMED, replica exchange. All open source and modifiable
- Support for Charmm36 switching functions. Set “vdwforceswitching on”
- Support for TIP4P
- Extended set of protocols in the ACEMDTK toolkit

Release acemd 2325 – Dec 2011
-----------------------------

**Main changes:**

- Even more ACEMD protocols, now distributed directly with ACEMD
- Added command extendsystem to read the box information from xsc file
- Added command pmegridspacing to set automatically the PME grid
- New PLUMED interface which does not require to pass box size
- New testsuite, every version of ACEMD is tested before release for energies, features, properties on over 20 test systems collected over the years

Release acemd 1890 – Feb 2011
-----------------------------

**Main changes:**

- Berendsen barostat now available
- Improved parallel performance
- Even faster with multiple time step scheme, now use for production runs “fullelectfrequency 2″ and timestep 4 as usual
- New constraints command to apply harmonic positional constraints on the GPU, much faster than the TCL version
- New plugin interface reporting box size (If you use your own PLUMED library, please recompile using the new interface)
- New pmegridspacing command to set automatically the pme grid size for each direction
- Launch of ACEMD protocols. The first one is ACETK.EQ available at http://www.multiscalelab.org/acemd/protocols/ACETK.EQ for the equilibration of molecular structures using ACEMD
- New mailing list at http://groups.google.com/group/acemd. Use acemd@googlegroups.com to post
- Compatibility extension to new CHARMM 36 forcefield formats (atom names longer than 3 letters)

Release acemd.1663 June 2010
----------------------------

- Optimized for cuda3.1 (you need to update drivers to 256.33 or greater)
- Optimized for Fermi
- Optimized further for PME grid sizes multiple of 2 (e.g. 64x64x64)
- Binaries for Centos and Fedora

Previous releases 2009
----------------------

- Use of PLUMED for biased free energy calculations
- Plugin interface for extending ACEMD with legacy code
- Large speed improvements
- Back up of restart files to avoid file corruptions
- Parallel version
- Improvements in the TCL interface
