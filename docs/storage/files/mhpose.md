---
title: ".mhpose file"
weight: 20
description: "MakeHuman Expression Pose file"
---

## .mhpose file
# MakeHuman Expression Pose file

## Overview

The `.mhpose` file format is used by MakeHuman to store facial expression poses. These files define expressions as a weighted blend of unit poses (pose units) from the face pose unit library.

## What is an .mhpose file?

An .mhpose file defines a facial expression in MakeHuman.

It stores a small set of bone adjustments that describe how facial bones move relative to their neutral (rest) position.

In simple terms, an .mhpose file describes how the face changes expression.

### What “expression” means in MakeHuman

An expression is a subtle, localized pose affecting mainly facial bones designed to be blended with other expressions

##### Examples:

- smile

- frown

- eyebrow raise

- mouth open

- phoneme shapes

## File Format

`.mhpose` files are JSON-formatted text files with UTF-8 encoding.

## Structure

A `.mhpose` file contains the following fields:

### Required Fields

- **`name`** (string): The display name of the expression
- **`unit_poses`** (object): A dictionary mapping pose unit names to their weight values
  - Keys: pose unit names (strings) from `face-poseunits.json`
  - Values: weight/influence values (float, typically 0.0 to 1.0)
  - Must contain at least one entry with a non-zero value

### Optional Metadata Fields

- **`description`** (string): A text description of the expression
- **`tags`** (array of strings): Tags for categorizing and searching expressions
- **`author`** (string): Creator of the expression
- **`copyright`** (string): Copyright information
- **`license`** (string): License under which the expression is distributed
- **`homepage`** (string): Website URL related to the expression author

## Example

```json
{
    "name": "Smirk",
    "description": "A subtle, knowing smirk - asymmetric smile with one eyebrow raised",
    "tags": [
        "smirk",
        "sly",
        "confident",
        "subtle"
    ],
    "unit_poses": {
        "MouthRightPullUp": 0.65,
        "MouthRightPullSide": 0.3,
        "MouthLeftPullUp": 0.2,
        "RightCheekUp": 0.4,
        "RightOuterBrowUp": 0.6,
        "RightInnerBrowUp": 0.3,
        "LeftBrowDown": 0.15
    },
    "author": "MakeHuman Community",
    "copyright": "© 2026 MakeHuman Community",
    "license": "CC0",
    "homepage": "http://www.makehumancommunity.org/"
}
```


### Example .mhpose file

[smile01.mhpose](smile01.mhpose)





[^1]: **Linear blend skinning**: A method for attaching a character’s surface to its skeleton, where each vertex follows a weighted average of the movements of its influencing bones.

