---
title: ".mhw file"
weight: 20
description: "MakeHuman Weights file"
---

## .mhw file
# MakeHuman Weights file

## Overview

The `.mhw` file format stores **vertex-to-bone weight assignments** for skeletal animation in MakeHuman. These files define how vertices of a mesh are influenced by bones in a skeleton, enabling smooth skeletal deformation during animation and posing.


### Purpose

- Defines vertex-to-bone weight assignments for linear blend skinning.[^1]

- Stores influence weights (0.0 to 1.0) indicating how much each bone affects each vertex

- Allows custom weight painting for different skeleton rigs
  
- Referenced by .mhskel files via the weights_file field 

### What a .mhw file contains

An .mhw file typically stores:

- Bone names

- Rotation values (and sometimes translations)

- Pose state relative to the rest pose

- Optional pose metadata

### How .mhw files are used

Typically used for:

- Static poses

- Pose libraries

- Previewing anatomy or clothing

- Exporting posed characters


##### Requirements:

.mhw files are skeleton (.mhskel) dependent.

- A compatible skeleton (.mhskel) is present

- Bone names and hierarchy match the target rig

- Rest pose assumptions must align

## File Structure

### Top-Level Properties

```json
{
    "copyright": "(c) 2021 Data Collection AB, Joel Palmius, Jonas Hauquier",
    "license": "CC0",
    "name": "MakeHuman weights",
    "description": "Symmetric weights for default makehuman mesh",
    "version": 110,
    "weights": {
        ...
    }
}
```

#### Metadata Fields

| Field | Type | Description |
|-------|------|-------------|
| `copyright` | string | Copyright information |
| `license` | string | License identifier (e.g., "CC0", "AGPL3") |
| `name` | string | Human-readable name of the weight set |
| `description` | string | Description of the weight data |
| `version` | integer | Version number (typically 110) |
| `weights` | object | Dictionary of bone weights (see below) |

### Weights Structure

The `weights` object maps bone names to arrays of vertex-weight pairs:

```json
"weights": {
    "bone_name_1": [
        [vertex_index, weight_value],
        [vertex_index, weight_value],
        ...
    ],
    "bone_name_2": [
        [vertex_index, weight_value],
        ...
    ]
}
```

#### Weight Entry Format

Each entry in the `weights` object:
- **Key**: `string` - Bone name (must match a bone name in the associated `.mhskel` file)
- **Value**: `array` - List of `[vertex_index, weight_value]` pairs

#### Vertex-Weight Pair Format

Each pair is a two-element array:
1. **Vertex Index** (`integer`): Zero-based index of the vertex in the mesh
2. **Weight Value** (`float`): Influence weight, typically in range [0.0, 1.0]

## Weight Assignment Rules

### Normalization

For each vertex, the sum of all weights across all bones **must equal 1.0**:

```
Σ(weights for vertex V across all bones) = 1.0
```

The `VertexBoneWeights` class automatically normalizes weights during loading:
- Weights are divided by the total weight sum for each vertex
- This ensures proper linear blend skinning

### Root Bone Assignment

Any vertices with **no weights** (sum = 0) are automatically assigned:
- **Bone**: Root bone (typically named "root" or first bone in skeleton)
- **Weight**: 1.0

This prevents vertices from being left unweighted.

### Weight Threshold

Weights below a threshold (default: `1e-4` or 0.0001) are **filtered out** during loading to:
- Reduce memory usage
- Improve performance
- Remove negligible influences

### Duplicate Handling

If the same vertex appears multiple times for the same bone:
- Weights are **merged (summed)**
- After normalization by total vertex weight

### Example .mhw file

[default_weights.mhw](default_weights.mhw)





[^1]: This is the footnote text.