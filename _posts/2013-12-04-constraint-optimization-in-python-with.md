---
layout: post
title: Constraint optimization in Python with Open Babel
date: '2013-12-04T10:36:00.000+01:00'
author: hellogetmyblogback
tags:
modified\_time: '2013-12-04T10:36:47.655+01:00'
blogger\_id: tag:blogger.com,1999:blog-8160351477288734008.post-948204056349719779
blogger\_orig\_url: https://combichem.blogspot.com/2013/12/constraint-optimization-in-python-with.html
---

This post is just a quick post to show, how you can optimize molecules with harmonic constraints in Python with Open Babel. This requires Open Babel to be compiled with Python SWIG-bindings.


There are three types of constraints, distances, angles and torsions. Here is a gist that might help get you started.

```python
import openbabel as ob

# Standard openbabel molecule load
conv = ob.OBConversion()
conv.SetInAndOutFormats('xyz','xyz')
mol = obOBMol()
conv.ReadFile(mol,'my_mol.xyz')

# Define constraints
constraints = ob.OBFFConstraints()
constraints.AddDistanceConstraint(1, 10, 3.4)       # Angstroms
constraints.AddAngleConstraint(1, 2, 3, 120.0)      # Degrees
constraints.AddTorsionConstraint(1, 2, 3, 4, 180.0) # Degrees

# Setup the force field with the constraints
forcefield = ob.OBForceField.FindForceField("MMFF94")
forcefield.Setup(mol, constraints)
forcefield.SetConstraints(constraints)

# Do a 500 steps conjugate gradient minimiazation
# and save the coordinates to mol.
forcefield.ConjugateGradients(500)
forcefield.GetCoordinates(mol)

# Write the mol to a file
conv.WriteFile(mol,'my_mol.xyz')
```


**Acknowledgements:** Kasper Thofte pretty much wrote the above gist back in the days.
