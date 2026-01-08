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

## Unit Poses

Unit poses are predefined atomic facial movements stored in the pose unit library:

- **Definition file**: `data/poseunits/face-poseunits.json` - maps frame names to descriptions
- **Animation file**: `data/poseunits/face-poseunits.bvh` - contains the actual pose data

Each unit pose represents a single, isolated facial muscle movement that can be combined with others.

### Available Unit Pose Names

The following unit pose names are available for use in `.mhpose` files:

**Cheek Controls:**

- `CheeksPump` - Puff out both cheeks
- `CheeksSuck` - Suck in both cheeks
- `LeftCheekUp` - Raise left cheek
- `RightCheekUp` - Raise right cheek

**Jaw and Chin Controls:**

- `JawDrop` - Open jaw/mouth
- `JawDropStretched` - Open jaw with stretched mouth
- `ChinRight` - Move chin to the right

**Brow Controls:**

- `LeftBrowDown` - Lower left eyebrow
- `LeftInnerBrowUp` - Raise left inner eyebrow
- `LeftOuterBrowUp` - Raise left outer eyebrow
- `RightBrowDown` - Lower right eyebrow
- `RightInnerBrowUp` - Raise right inner eyebrow
- `RightOuterBrowUp` - Raise right outer eyebrow

**Eye Controls:**

- `LeftEyeUp` - Look up with left eye
- `LeftEyeturnRight` - Turn left eye to the right
- `LeftUpperLidOpen` - Open left upper eyelid
- `LeftUpperLidClosed` - Close left upper eyelid
- `LeftLowerLidUp` - Raise left lower eyelid
- `RightEyeUp` - Look up with right eye
- `RightEyeturnRight` - Turn right eye to the right
- `RightUpperLidOpen` - Open right upper eyelid
- `RightUpperLidClosed` - Close right upper eyelid
- `RightLowerLidUp` - Raise right lower eyelid

**Mouth Movement Controls:**

- `MouthMoveLeft` - Move mouth to the left
- `MouthLeftPullDown` - Pull left corner of mouth down
- `MouthLeftPullSide` - Pull left corner of mouth to the side
- `MouthLeftPullUp` - Pull left corner of mouth up (smile)
- `MouthRightPullDown` - Pull right corner of mouth down
- `MouthRightPullSide` - Pull right corner of mouth to the side
- `MouthRightPullUp` - Pull right corner of mouth up (smile)
- `MouthLeftPlatysma` - Activate left platysma muscle (neck tension)
- `MouthRightPlatysma` - Activate right platysma muscle (neck tension)

**Lip Controls:**

- `LipsKiss` - Pucker lips for kissing
- `UpperLipUp` - Raise upper lip
- `UpperLipForward` - Push upper lip forward
- `UpperLipBackward` - Pull upper lip backward
- `UpperLipStretched` - Stretch upper lip horizontally
- `lowerLipUp` - Raise lower lip
- `lowerLipDown` - Lower lower lip
- `lowerLipForward` - Push lower lip forward
- `lowerLipBackward` - Pull lower lip backward

**Nose and Face Controls:**

- `NoseWrinkler` - Wrinkle/scrunch nose
- `NasolabialDeepener` - Deepen nasolabial folds (smile lines)

## Weight Values

- Weights typically range from **0.0** (no influence) to **1.0** (full influence)
- Values can exceed 1.0 for exaggerated effects
- Only non-zero weights need to be specified in the `unit_poses` dictionary
- The final expression is computed as a weighted linear blend of the specified unit poses

## Usage in MakeHuman

### Loading Expressions

Expressions can be loaded through:

1. **Pose/Animate > Expressions** tab - Browse and select `.mhpose` files
2. **MakeHuman file format** (`.mhm`) - Saved with the character using:

   ```text
   expression <relative_path_to_mhpose_file>
   ```

### Creating Expressions

New expressions can be created using:

- **Utilities > Expression Mixer** tool
  - Adjust sliders for individual unit poses
  - Combine multiple unit poses with different weights
  - Save the result as a new `.mhpose` file

### File Locations

Default search paths for `.mhpose` files:

- \<UserDataDir\>/data/expressions/
- \<SystemDataDir\>/data/expressions/


### Example .mhpose file

[smile01.mhpose](smile01.mhpose)





[^1]: **Linear blend skinning**: A method for attaching a character’s surface to its skeleton, where each vertex follows a weighted average of the movements of its influencing bones.

