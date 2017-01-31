---
title: ACEMD 3 Beta Specifying restraints
layout: default
name: ACEMD 3 Beta Documentation
description: ACEMD 3 Beta Command Documentation
---

# Introduction

In earlier versions of ACEMD applying restraining potentials to the system required TCL scripting. Starting with this Beta, ACEMD now has support for directly defining restraints without neededing any scripting.


Two classes of restraint are currently supported:

* Atom-centred Restraints
 Defined with the command ```atomRestraint```. These restraints apply a flat-bottomed harmonic potential to restrain individual atoms about a reference coordinate.

* Group-based Restraaints
 Defined with the command ```groupRestraint```. These restraints apply a flat-bottomed harmonic potential to restraint a group of atoms about a reference coordinate.

# Command Syntax

```
{atom,group}Restraint "[atom selection]" axes {xyz} width [l] setpoints [k@t] ...
```

## atom selection

Each restraint type requires an atom selection (in VMD atom selection grammar) to define the atoms upon which the restraint will be applied. The selection also defines the reference coordinates for the restraint, made against the geometry in the ```coordinates````PDB file.

* For an atomRestraint, each individial atom in the selection is restrained about its initial coordinate in the PDB by a flat-bottomed harmonic potential. 

* For a groupRestraint, the atoms in the selection are restrained about their centre of mass in the initial PDB by a flat-bottomed harmonic potential.

## axes

Specifies the axes along which the restraint will apply. The default is "xyz", indicating  a 3D restraint. To apply a restraint solely in the Z direction, allowing particles to diffuse freely in an X-Y plane, use "axes z". 

This keyword is optional. If ommited the default is assumed.

## width

Specifies the width of the flat bottom of the restraining potential in Angstrom.When set the same width is applied to each dimension that the restraint is active for (see 'axes').

This keyword is optional. If ommited the default is assumed.

* For an atomRestraint the default value is 0. 

* For a groupRestraint the default value is the linear extent of the atom selection plus 4 Angstroms. 

## setpoints

Setpoints define the spring constant 'k' for the restraint at time 't'. When multiple setpoints may be defined ACEMD will linearly ramp 'k' between them, at ```trajectoryFreq``` intervals (default 100ps).

The syntax is:

```
k@t
```

Where 'k' is in kcal/mol and 't' is in timestep or time if one of the  suffices 'us', 'ns', 'ps', 'fs' is used.

For example, a 10 kcal/mol restraint applied from the beginning of the simulation, gradually reduced to 0 at 100ns may be defined with:

```
setpoints 10@0ns 0@100ns
```
