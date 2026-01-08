---
title: ".mhskel file"
weight: 20
description: "MakeHuman Skeleton file"
---

## .mhskel file
# MakeHuman Skeleton file

## Overview

The .mhskel file format is a JSON-based skeleton/rig definition file used by MakeHuman to define animation armatures. These files describe hierarchical bone structures that can be applied to the MakeHuman base mesh for animation, posing, and export to external 3D applications.

## What is an .mhskel file?

An .mhskel file defines a skeleton (rig) for a MakeHuman character.

An .mhskel file encodes:

- Which bones exist. Bone names and hierarchy with connections via parent–child relationships
  
- Where they are positioned in the neutral (rest) pose

- Rest-pose bone transforms

- Semantic roles (e.g. deform, helper, control)

- Metadata used by exporters and modifiers

Think of it as the blueprint for a rig.

## What an .mhskel file is used for

When you choose a skeleton in MakeHuman, the program uses an .mhskel file to:

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

## Data model

### Core concepts

An .mhskel file defines:

- Bones

- Hierarchy

- Rest pose

- Semantic roles

- Attachment points to the character

Each of these is independent, which allows skeletons to remain reusable across characters and exports.

#### Bones

Each bone entry defines:

- A unique bone name

- A parent bone (except for the root)

- A rest-pose transform

The rest pose specifies:

- Position relative to the parent

- Orientation

- Bone length or endpoint

Bones are defined in local space, not world space.

#### Hierarchy

Bones are organized into a tree structure:

Each bone has exactly one parent (except the root)

Transform inheritance flows from parent to child

Moving a parent implicitly moves all descendants

The hierarchy defines:

- Joint behavior

- How rotations propagate

- Which parts of the body move together


#### Rest pose

The rest pose describes the neutral skeleton state:

- No animation applied

- No deformation

- Standard reference orientation

All animation, posing, and export transformations are applied relative to this rest pose.


#### Semantic roles

Bones in an .mhskel file may carry semantic tags, such as:

- Deformation bones (affect the mesh)

- Helper or alignment bones

- Control or reference bones

These tags are used by:

- Modifiers

- Exporters

- Tooling logic
  
##### Tooling logic

When an .mhskel file assigns semantic roles to bones, tooling logic uses those roles to decide things like:

- Which bones are allowed to deform the mesh

- Which bones are hidden from the user

- Which bones are exported or omitted

- How bones are grouped or named in the UI

- How exporters map bones to external formats

- Which bones are treated as helpers or references


#### Character attachment model

An .mhskel file defines how bones are anchored to the character via:

- Named reference points

- Helper geometry mappings

- Proportional relationships

During skeleton application:

- Bones are positioned based on the current character shape

- Skinning weights are generated or adapted afterward

This separation allows the same skeleton to fit many characters.

## Location
.mhskel files are stored in the user or system data directory under /rigs/

\<UserDataPath\>/data/rigs/\<mesh_name\>/

\<SystemDataPath\>/data/rigs/\<mesh_name\>/

### File Structure

The file is a JSON document with the following top-level structure:

```json
{
    "name": "Skeleton Name",
    "version": 110,
    "description": "Description of the skeleton purpose and features",
    "copyright": "Copyright information",
    "license": "License identifier (e.g., CC0, AGPL3)",
    "plane_map_strategy": 3,
    "weights_file": "skeleton_weights.mhw",
    "bones": { ... },
    "joints": { ... },
    "planes": { ... }
}
```

### Top-Level Fields

#### Metadata

**name** (string, required): Human-readable name of the skeleton
**version** (integer, required): Skeleton format version number (current: 110)
**description** (string, optional): Detailed description of the skeleton's purpose, features, and intended use cases
**copyright** (string, optional): Copyright information for the skeleton definition license (string, optional): License under which the skeleton is distributed (e.g., "CC0", "AGPL3")

#### Configuration

**plane_map_strategy** (integer, optional, default: 3): Strategy for mapping rotation planes from reference skeleton:

1: Use first reference bone only
2: Use last reference bone only
3: Average the normal of all reference bone planes (recommended)

**weights_file** (string, optional): Relative path to associated .mhw (MakeHuman Weights) file containing vertex weight assignments for skinning

#### Bones

The "bones" object defines the skeleton hierarchy. Each key is a bone name, and the value is a bone definition object.

##### Bone Definition Object

```json
"bone_name": {
    "head": "joint_name_for_head",
    "tail": "joint_name_for_tail",
    "parent": "parent_bone_name",
    "reference": ["ref_bone1", "ref_bone2"],
    "weights_reference": ["weight_ref_bone1", "weight_ref_bone2"],
    "rotation_plane": "plane_name"
}
```

##### Fields

**head** (string, required): Joint name defining the start/root position of the bone
**tail** (string, required): Joint name defining the end/tip position of the bone
**parent** (string, optional): Name of the parent bone in the hierarchy. Omit or set to null for root bones
**reference** (array of strings or null, optional): List of bone names from the reference skeleton that this bone maps to for pose transformation purposes
**weights_reference** (array of strings, optional): List of bone names from the reference skeleton to use for vertex weight mapping. If omitted, uses the reference field or attempts implicit mapping by bone name
**rotation_plane** (string or array, optional): Name of the plane(s) used to calculate the bone's roll angle (rotation around its local Y-axis)

##### Naming Conventions:

Bilateral bones use .L and .R suffixes for left and right sides

#### Joints

The "joints" object maps joint names to vertex indices on the base mesh. Joint positions are calculated as the average position of these vertices.

```json
"joints": {
    "joint_name": [vertex_index1, vertex_index2, vertex_index3, ...],
    "another_joint": [vertex_index1, vertex_index2]
}
```

##### Fields

**Key** (string): Unique joint identifier (referenced by bones' head and tail fields)
**Value** (array of integers): List of vertex indices from the base mesh. The joint position is computed as the centroid of these vertices

##### Purpose

Allows skeleton to automatically adapt to mesh modifications

Ensures bone positions follow anatomical landmarks on the mesh

Single-vertex joints (arrays with one element) use that exact vertex position

#### Planes

The "planes" object defines orientation planes used to calculate bone roll angles. A plane is defined by three joint positions.

```json
"planes": {
    "plane_name": ["joint1", "joint2", "joint3"],
    "another_plane": ["joint_a", "joint_b", "joint_c"]
}
```

##### Fields

**Key** (string): Unique plane identifier (referenced by bones' rotation_plane field)
**Value** (array of 3 strings): Three joint names that define the plane. The normal vector of this plane determines the bone's roll angle

##### Purpose

Controls how bones rotate around their length axis (roll/twist)

Essential for proper elbow, knee, and finger bending

Ensures natural deformation during animation

### Usage Context

#### Within MakeHuman:

The default reference skeleton is default.mhskel

Custom skeletons can reference the default skeleton for weight and pose mapping

Not used for internal posing (MakeHuman uses its base skeleton for that)

#### For Export
Selected skeleton is exported with the model to external applications

Supports various export formats (FBX, Collada, glTF, etc.)

Provides animation-ready rigs for Blender, Unity, Unreal Engine, etc.

#### Weight Mapping
If a skeleton doesn't have explicit weights, they're remapped from the reference skeleton

Uses reference bones to map pose transformations

Uses weights_reference bones to map vertex skinning weights

### Related Files
**.mhw** (MakeHuman Weights): Binary file containing vertex-to-bone weight assignments
**.mhpose**: Pose files that can be applied to skeletons
**.bvh**: BVH animation files that can be retargeted to skeletons

### Example .mhskel file

[default.mhskel](default.mhskel)



