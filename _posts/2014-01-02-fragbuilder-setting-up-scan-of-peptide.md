---
layout: post
title: 'FragBuilder: Setting up a scan of peptide conformations.'
date: '2014-01-02T17:09:00.000+01:00'
author: hellogetmyblogback
tags:
modified\_time: '2014-01-02T17:09:01.506+01:00'
blogger\_id: tag:blogger.com,1999:blog-8160351477288734008.post-8831992765893305854
blogger\_orig\_url: https://combichem.blogspot.com/2014/01/fragbuilder-setting-up-scan-of-peptide.html
---

This post covers the essentials of how to set up a scan of different peptide conformations. We have used this method to calculate more than 1.5M QM calculation on model peptides in our group.



In this example we make a tri-glycine peptide and scan over the phi/psi torsion angles between -180 and 180 degrees with 60 degrees spacing.



For each conformation, the MMFF94 force field energy is printed and the structure is saved as an XYZ file.

```python

from fragbuilder import Peptide

# Create a peptide object with the sequence
# glycine-glycine-glycine.
sequence = "GGG"
pep = Peptide(sequence)

# Define a list of angles.
angles = range(-180, 180, 60)

# Double loop over phi/psi angles in the desired range.
for psi in angles:
  for phi in angles:

    # Set the second (leucine) residue to (phi, psi).
    pep.set_bb_angles(2, [phi, psi])

    # Print the energy of the peptide in this state.
    # This is of course just an example.
    print pep.get_energy()

    # Save each conformation.
    pep.write_xyz("pep_%04i_%04i.xyz" % (phi, psi))
```
