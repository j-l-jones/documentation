---
title: ".mhm file"
weight: 20
description: "MakeHuman model file"
---

## .mhm file
# MakeHuman model file

## Overview

### What is an .mhm file?

An .mhm file stores parameters that define a character but not the geometry itself, rather like a recipe.

### Data model

Internally, an .mhm is a plain text file that records:

- Scalar values for macro- and micro-parameters

- Target activations and weights

- Configuration switches (modifiers, assets, materials)

- References to external target and asset definitions

- Body proportions (height, weight, gender balance, etc.)

- Applied targets and their values

- Skin, materials, and other appearance settings

- Modifier and configuration choices


### How .mhm files are used

When you load a .mhm file, MakeHuman2:

- Starts from the base human model

- Applies the stored parameters

- Reconstructs the character procedurally

### Creation
.mhm files are generated via File -> Save and stored in the user data directory under /models/

\<UserDataPath\>/data/models/\<mesh_name\>/
  

<img src="file-save-mhm.png" alt="MHM file save dialog" width="400">

### Example .mhm file

\# MakeHuman2 Model File
version v2.0.1\
name foo\
author foo\
uuid 4315e863-03d7-44cd-a207-b2d797ee54e7\
tags example;foo\
eyes HighPolyEyes 2c12f43b-1303-432c-b7ce-d78346baf2e6\
clothes female_casualsuit01 a8b0e841-144b-4f03-8b9e-ea8fdce8c863\
clothes fedora 566dbd52-71d1-4d76-a799-0474a5a384db\
hair bob02 cd7840cf-b340-49e9-a505-8a5a22a86db2\
skinMaterial skins/default.mhmat\
material HighPolyEyes 2c12f43b-1303-432c-b7ce-d78346baf2e6 eyes/materials/brown.mhmat



