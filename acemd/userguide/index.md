---
title: ACEMD User Guide
layout: default
name: User Guide
description: ACEMD user guide
---

Content
=======

1 auto-gen TOC:
{:toc}

ACEMD
================

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

