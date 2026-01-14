---
title: "body-poseunits"
weight: 20
description: "MakeHuman Body Pose Units"
---

## body-poseunits.json
# MakeHuman Body Pose Units

## Overview

MakeHuman's body posing system uses **pose units** - individual, isolated body movements that can be combined to create complex poses. Unlike face pose units (which use BVH files), body pose units are stored **directly as quaternion rotations** in a single JSON file.

### High-level structure

The file has two top-level fields:

- name: a label for the pose-unit set

- poses: a dictionary mapping pose-unit name → bone rotations 


**Key Differences from Face Pose Units:**

| Aspect | Face Pose Units | Body Pose Units |
| ------ | --------------- | --------------- |
| Format | JSON + BVH (two files) | JSON only |
| Storage | BVH frames with rotation data | Direct quaternion values per bone |
| Structure | Frame-based | Pose-based (named poses) |

## File Location

```text
makehuman/data/poseunits/
└── body-poseunits.json
```
---

## File Format

### Structure

```json
{
    "name": "Body",
    "poses": {
        "PoseName1": {
            "bone_name1": [w, x, y, z],
            "bone_name2": [w, x, y, z],
            ...
        },
        "PoseName2": {
            "bone_name1": [w, x, y, z],
            ...
        },
        ...
    }
}
```

### Pose Entry Structure

Each pose is a dictionary where:
- **Key:** Bone name (string) - MakeHuman bone identifier
- **Value:** Quaternion array `[w, x, y, z]` - Rotation for that bone

### Quaternion Format

Quaternions are represented as 4-element arrays: **`[w, x, y, z]`**

Where:
- **`w`** = Scalar (real) part
- **`x, y, z`** = Vector (imaginary) parts

**Important:** MakeHuman uses **`[w, x, y, z]`** order (scalar-first), not `[x, y, z, w]`.

#### Example Quaternion Values

```json
"UpperArmUpLeft1": {
    "oris02": [0.993819, -0.0954232, 0, 0.0152616],
    "oris01": [0.973121, -0.200122, 0, 0],
    "special02": [0.999808, 0.0162821, 0, 0.00510081]
}
```

This defines:
- Bone `oris02` rotates with quaternion `(w=0.993819, x=-0.0954232, y=0, z=0.0152616)`
- Bone `oris01` rotates with quaternion `(w=0.973121, x=-0.200122, y=0, z=0)`
- Bone `special02` rotates with quaternion `(w=0.999808, x=0.0162821, y=0, z=0.00510081)`

---

## Available Pose Units

The standard body-poseunits.json includes pose units organized by body region:

### Upper Arm (Left) - 12 poses

**Vertical Movement:**
- `UpperArmUpLeft1` - Raise left upper arm (stage 1)
- `UpperArmUpLeft2` - Raise left upper arm (stage 2)
- `UpperArmDownLeft` - Lower left upper arm

**Forward/Backward:**
- `UpperArmForwardLeft` - Move left upper arm forward
- `UpperArmBackwardLeft` - Move left upper arm backward

**Roll/Rotation:**
- `UpperArmRollOutLeft` - Roll left upper arm outward
- `UpperArmRollInLeft` - Roll left upper arm inward

### Lower Arm (Left) - 4 poses

**Bending:**
- `LowerArmBend1Left1` - Bend left lower arm (stage 1, variant 1)
- `LowerArmBend1Left2` - Bend left lower arm (stage 1, variant 2)

### Hand (Left) - 6 poses

**Roll:**
- `HandRollBackwardLeft` - Roll left hand backward
- `HandRollForwardLeft` - Roll left hand forward

**Bending:**
- `HandBendInLeft` - Bend left hand inward
- `HandBendOutLeft` - Bend left hand outward
- `HandBendUpLeft` - Bend left hand up
- `HandDownLeft` - Bend left hand down

**Special:**
- `HeadTurnRight` - Turn hand/wrist right (affects finger metacarpals)
- `HeadTurnLeft` - Turn hand/wrist left (affects finger metacarpals)

### Head and Neck - 6 poses

**Rotation:**
- `HeadTurnRight` - Turn head to the right
- `HeadTurnLeft` - Turn head to the left

**Vertical:**
- `HeadUp` - Tilt head up
- `HeadDown` - Tilt head down

**Horizontal:**
- `HeadLeft` - Tilt head to left
- `HeadRight` - Tilt head to right

### Torso - 6 poses

**Vertical:**
- `TorsoUp` - Bend torso upward
- `TorsoDown` - Bend torso downward

**Horizontal:**
- `TorsoLeft` - Bend torso to left
- `TorsoRight` - Bend torso to right

### Upper Leg (Left) - 8 poses

**Vertical:**
- `UpperLegUpLeft` - Raise left upper leg
- `UpperLegDownLeft` - Lower left upper leg

**Forward/Backward:**
- `UpperLegForwardLeft` - Move left upper leg forward
- `UpperLegBackwardLeft` - Move left upper leg backward

**Roll/Rotation:**
- `UpperLegRollOutLeft` - Roll left upper leg outward
- `UpperLegRollInLeft` - Roll left upper leg inward

### Lower Leg (Left) - 2 poses

**Bending:**
- `LowerLegBendLeft1` - Bend left lower leg (stage 1)
- `LowerLegBendLeft2` - Bend left lower leg (stage 2)

### Foot (Left) - 8 poses

**Rotation:**
- `FootTurnOutLeft` - Turn left foot outward
- `FootTurnInLeft` - Turn left foot inward

**Roll:**
- `FootRollInLeft` - Roll left foot inward
- `FootRollOutLeft` - Roll left foot outward

**Vertical:**
- `FootDownLeft` - Point left foot down
- `FootUpLeft` - Point left foot up

### Fingers (Left) - 10 poses

**Close/Open:**
- `Finger1CloseLeft` - Close finger 1 (thumb area)
- `Finger2CloseLeft` - Close finger 2
- `Finger3CloseLeft` - Close finger 3 (index)
- `Finger4CloseLeft` - Close finger 4 (middle)
- `Finger5CloseLeft` - Close finger 5 (ring)
- `Finger1OpenLeft` - Open finger 1 (pinky)
- `Finger2OpenLeft` - Open finger 2
- `Finger3OpenLeft` - Open finger 3 (index)
- `Finger4OpenLeft` - Open finger 4 (middle)
- `Finger5OpenLeft` - Open finger 5 (ring)

**Roll:**
- `Figer1RollInLeft` - Roll finger 1 inward (note: typo in name)
- `Figer1RollOutLeft` - Roll finger 1 outward (note: typo in name)

### Toes (Left) - 11 poses

**Close:**
- `Toe1CloseLeft` - Close toe 1
- `Toe2CloseLeft` - Close toe 2
- `Toe3CloseLeft` - Close toe 3 (big toe)
- `Toe4CloseLeft` - Close toe 4
- `Toe5CloseLeft` - Close toe 5

**Open:**
- `Toe1OpenLeft` - Open toe 1 (pinky toe)
- `Toe6OpenLeft` - Open toe 6 (alternative)
- `Toe7OpenLeft` - Open toe 7 (alternative)
- `Toe8OpenLeft` - Open toe 8 (big toe, alternative)
- `Toe9OpenLeft` - Open toe 9 (alternative)

**Note:** Right-side poses follow the same pattern with `.R` suffix instead of `.L`.

---
## Technical Details

### Quaternion Mathematics

#### Why Quaternions?

Quaternions are preferred for 3D rotations because:
- **No gimbal lock** - Unlike Euler angles
- **Smooth interpolation** - SLERP provides smooth rotation blending
- **Compact** - 4 values vs 9 for rotation matrix
- **Efficient** - Faster than matrix operations for blending


### Related Files

**face-poseunits.json** and **face-poseunits.bvh** Definitions for facial bone movements that MakeHuman combines to create expressions.


### Example

[body-poseunits.json](body-poseunits.json)





