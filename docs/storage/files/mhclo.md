---
title: ".mhclo file"
weight: 20
description: "MakeHuman Clothing file"
---

## .mhclo file
# MakeHuman Clothing file

## Overview

## What is an .mhclo file?

An .mhclo file defines a piece of clothing for MakeHuman.

It tells MakeHuman how a clothing mesh should attach to and deform with a character, across different body shapes.

The .mhclo file is the definition — the actual clothing geometry lives in a separate mesh file.

## What an .mhclo file contains

An .mhclo file typically describes:

- Which mesh file represents the clothing

- How the clothing follows body shape changes

- Which body parts the clothing depends on

- Optional masking or exclusion rules

- Metadata used by the UI and exporters

## How .mhclo files are used

When clothing is applied:

- The character shape is evaluated

- The clothing mesh is loaded

- The clothing is fitted to the body

- Deformation rules are applied so the clothing follows shape changes

- The clothing becomes part of the exportable character

## How .mhclo files are created

There are two common cases:

#### Supplied clothing

- Authored by MakeHuman contributors

- Tested across many shapes

- Distributed with the application

####  Custom clothing (advanced)

Clothing mesh created in external software

.mhclo file written to describe how it behaves

Requires understanding of MakeHuman’s clothing system

This is an advanced workflow and not required for most users.

When users should care

You’ll encounter .mhclo files when you:

Add or remove clothing

Install custom clothing assets

Export clothed characters

Most users never need to edit .mhclo files manually.

Simple mental model

A .mhclo file tells MakeHuman how clothing behaves, not just what it looks like.

.mhm → character definition

.mhskel → skeleton definition

.mhclo → clothing definition

Export → final assets

An .mhskel file defines a skeleton (rig) for a MakeHuman character.

An .mhskel file encodes:

- Which bones exist. Bone names and hierarchy with connections via parent–child relationships
  
- Where they are positioned in the neutral (rest) pose

- Rest-pose bone transforms

- Semantic roles (e.g. deform, helper, control)

- Metadata used by exporters and modifiers

Think of it as the blueprint for a rig.

## What a .mhskel file is used for

When you choose a skeleton in MakeHuman, the program uses a .mhskel file to:

- Create the character’s bone structure

- Fit the bones to the current body shape

- Prepare the character for posing, animation, or export

## How .mhskel is used

<img src="mhskel-usage.png" alt="MHM file save dialog" width="400">

When a skeleton is applied:

- The .mhskel definition is loaded

- Bones are instantiated relative to the character

- Internal mappings connect bones to helper geometry

- The bone-to-mesh influence weights are computed so the mesh deforms correctly when the skeleton moves.

- An export-ready armature is produced

This allows the same character to be exported with different skeletons without changing the .mhm file.

### Data model

#### Core concepts

An .mhskel file defines:

- Bones

- Hierarchy

- Rest pose

- Semantic roles

- Attachment points to the character

Each of these is independent, which allows skeletons to remain reusable across characters and exports.

##### Bones

Each bone entry defines:

- A unique bone name

- A parent bone (except for the root)

- A rest-pose transform

The rest pose specifies:

- Position relative to the parent

- Orientation

- Bone length or endpoint

Bones are defined in local space, not world space.

##### Hierarchy

Bones are organized into a tree structure:

Each bone has exactly one parent (except the root)

Transform inheritance flows from parent to child

Moving a parent implicitly moves all descendants

The hierarchy defines:

- Joint behavior

- How rotations propagate

- Which parts of the body move together


##### Rest pose

The rest pose describes the neutral skeleton state:

- No animation applied

- No deformation

- Standard reference orientation

All animation, posing, and export transformations are applied relative to this rest pose.


##### Semantic roles

Bones in a .mhskel file may carry semantic tags, such as:

- Deformation bones (affect the mesh)

- Helper or alignment bones

- Control or reference bones

These tags are used by:

- Modifiers

- Exporters

- Tooling logic
  
###### Tooling logic

When a .mhskel file assigns semantic roles to bones, tooling logic uses those roles to decide things like:

- Which bones are allowed to deform the mesh

- Which bones are hidden from the user

- Which bones are exported or omitted

- How bones are grouped or named in the UI

- How exporters map bones to external formats

- Which bones are treated as helpers or references


##### Character attachment model

A .mhskel file defines how bones are anchored to the character via:

- Named reference points

- Helper geometry mappings

- Proportional relationships

During skeleton application:

- Bones are positioned based on the current character shape

- Skinning weights are generated or adapted afterward

This separation allows the same skeleton to fit many characters.

### Location
.mhskel files are stored in the user data directory under /rigs/

\<UserDataPath\>/data/rigs/\<mesh_name\>/


### Example .mhskel file

[default.mhskel](default.mhskel)



