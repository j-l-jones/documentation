---
title: "face-poseunits"
weight: 20
description: "MakeHuman Expression Pose Units"
---

## face-poseunits.json
# MakeHuman Expression Pose Units

## Overview

MakeHuman's facial expression system uses **pose units** - small atomic facial movements that can be combined to create complex expressions.

Examples of pose units:

- raising an eyebrow

- pulling a mouth corner

- opening the jaw slightly

- narrowing the eyes

Each unit pose represents a single, isolated facial muscle movement that can be combined with others.

The system consists of two complementary files that work together:

1. **`face-poseunits.json`** - Defines the names and metadata which maps frame names to descriptions
2. **`face-poseunits.bvh`** - Contains the actual 3D skeletal transformations

Together, face-poseunits.json and face-poseunits.bvh define the building blocks for facial expressions in MakeHuman.

They provide the low-level data that allows facial expressions to be:

- reusable

- blendable

- shape-independent

You can think of them as the foundation layer beneath the Expression Mixer.

---


## face-poseunits.json

### What it contains

face-poseunits.json provides the semantic layer.

It describes:

- the names of pose units

- how they are grouped (e.g. brows, mouth, eyes)

- how they are exposed in the UI

- optional metadata such as symmetry or mirroring

The JSON file tells MakeHuman:

- which pose units exist

- how they should be combined

- where they appear in tools like the Expression Mixer

### Structure

```json
{
    "framemapping": [
        "Rest",
        "LeftBrowDown",
        "RightBrowDown",
        ...
    ],
    "copyright": "(c) 2020 Data Collection AB, Joel Palmius, Jonas Hauquier",
    "license": "CC0",
    "name": "Standard face pose units",
    "version": 110,
    "animation_file": "poseunits.bvh",
    "homepage": "www.makehumancommunity.org",
    "author": "MakeHuman"
}
```

### Fields

| Field | Type | Description |
|-------|------|-------------|
| `framemapping` | Array of strings | Ordered list of pose unit names. The array index corresponds to the frame number in the BVH file. |
| `copyright` | String | Copyright information |
| `license` | String | License type (typically CC0) |
| `name` | String | Descriptive name of the pose unit set |
| `version` | Integer | Version number |
| `animation_file` | String | Reference to the associated BVH file |
| `homepage` | String | Project homepage |
| `author` | String | Author/creator information |

### Frame Mapping

The `framemapping` array establishes the relationship between pose unit names and BVH frame numbers:

```
Index 0  → Frame 0  → "Rest"
Index 1  → Frame 1  → "LeftBrowDown"
Index 2  → Frame 2  → "RightBrowDown"
...
Index 59 → Frame 59 → "TonguePointDown"
```

**Important:** The array index is zero-based and directly corresponds to the frame number in the BVH file.


### Relationship to the BVH file

The JSON file references pose units defined in the BVH file.

In other words:

BVH → geometry and motion data

JSON → meaning, organization, and presentation

---

## face-poseunits.bvh

### What is BVH?

BVH (BioVision Hierarchy) is a motion capture file format that stores:
- **Skeleton hierarchy** - The structure of bones/joints
- **Motion data** - Rotation and position values for each bone over time
 
#### What it contains

face-poseunits.bvh stores the actual facial bone poses.

Specifically:

- a skeleton definition (limited to facial bones)

- a rest pose

- a series of named static poses

- one pose per pose unit

Each pose encodes:

- which facial bones move

- how much they rotate or translate

- relative to the neutral face pose

Although BVH is often associated with animation, here it is used as a compact container for pose data, not motion over time.

### File Structure

```
HIERARCHY
ROOT root
{
    OFFSET 0.000000 0.639400 0.631400
    CHANNELS 6 Xposition Yposition Zposition Xrotation Yrotation Zrotation
    JOINT spine05
    {
        OFFSET 0.000000 -0.790650 0.041950
        CHANNELS 3 Xrotation Yrotation Zrotation
        ...
    }
    ...
}
MOTION
Frames: 60
Frame Time: 0.041667
[frame 0 data: rotation values for all bones]
[frame 1 data: rotation values for all bones]
...
[frame 59 data: rotation values for all bones]
```

### Key Components

#### 1. HIERARCHY Section

Defines the skeletal structure of the face:
- **ROOT** - The base node (usually `root`)
- **JOINTS** - Facial bones/control points (levator muscles, jaw, etc.)
- **OFFSET** - Position offset from parent joint
- **CHANNELS** - Which transformations are animated (position and/or rotation)

Example facial joints:
- `head` - Main head bone
- `levator02.L`, `levator03.L` - Left eyebrow control
- `levator02.R`, `levator03.R` - Right eyebrow control
- `jaw` - Jaw bone for mouth opening
- Various tongue, lip, and cheek control joints

#### 2. MOTION Section

Contains the actual pose data:
- **Frames:** Number of frames (should match `framemapping` array length)
- **Frame Time:** Time per frame in seconds (typically 0.041667 = 24fps)
- **Frame Data:** Each line is one frame with space-separated rotation values

### Motion Data Format

Each frame line contains rotation/position values for **all** channels defined in the hierarchy:

```
X Y Z Xrot Yrot Zrot [joint1_Xrot joint1_Yrot joint1_Zrot] [joint2_Xrot ...]
```

For face-poseunits.bvh with ~200+ bones and 3 rotation channels each, each line has 600+ numerical values.

### Example: .mhpose File Usage

```json
{
    "unit_poses": {
        "LeftBrowDown": 0.8,
        "RightBrowDown": 0.3,
        "LipsKiss": 0.5
    }
}
```

This blends:
- Frame 1 (LeftBrowDown) at 80% strength
- Frame 2 (RightBrowDown) at 30% strength  
- Frame 41 (LipsKiss) at 50% strength

The result is a weighted combination of all three skeletal poses.

---

## Available Pose Units

The standard face-poseunits.json (v110) includes 60 pose units:

### Eyebrow Controls (6)
- `LeftBrowDown` - Lower left eyebrow
- `RightBrowDown` - Lower right eyebrow
- `LeftOuterBrowUp` - Raise left outer eyebrow
- `RightOuterBrowUp` - Raise right outer eyebrow
- `LeftInnerBrowUp` - Raise left inner eyebrow
- `RightInnerBrowUp` - Raise right inner eyebrow

### Eye Controls (14)
- `LeftUpperLidOpen` - Open left upper eyelid
- `RightUpperLidOpen` - Open right upper eyelid
- `LeftUpperLidClosed` - Close left upper eyelid
- `RightUpperLidClosed` - Close right upper eyelid
- `LeftLowerLidUp` - Raise left lower eyelid
- `RightLowerLidUp` - Raise right lower eyelid
- `LeftEyeDown` - Look down (left eye)
- `RightEyeDown` - Look down (right eye)
- `LeftEyeUp` - Look up (left eye)
- `RightEyeUp` - Look up (right eye)
- `LeftEyeturnRight` - Turn left eye right
- `RightEyeturnRight` - Turn right eye right
- `LeftEyeturnLeft` - Turn left eye left
- `RightEyeturnLeft` - Turn right eye left

### Nose and Cheek Controls (6)
- `NoseWrinkler` - Wrinkle nose
- `LeftCheekUp` - Raise left cheek
- `RightCheekUp` - Raise right cheek
- `CheeksPump` - Puff out both cheeks
- `CheeksSuck` - Suck in both cheeks
- `NasolabialDeepener` - Deepen nasolabial folds

### Jaw and Chin Controls (5)
- `ChinLeft` - Move chin left
- `ChinRight` - Move chin right
- `ChinDown` - Move chin down
- `ChinForward` - Push chin forward
- `JawDrop` - Open jaw/mouth
- `JawDropStretched` - Open jaw with stretched mouth

### Lip Controls (11)
- `lowerLipUp` - Raise lower lip
- `lowerLipDown` - Lower lower lip
- `lowerLipBackward` - Pull lower lip backward
- `lowerLipForward` - Push lower lip forward
- `UpperLipUp` - Raise upper lip
- `UpperLipBackward` - Pull upper lip backward
- `UpperLipForward` - Push upper lip forward
- `UpperLipStretched` - Stretch upper lip
- `LipsKiss` - Pucker lips for kissing
- `MouthMoveLeft` - Move mouth to left
- `MouthMoveRight` - Move mouth to right

### Mouth Corner Controls (6)
- `MouthLeftPullUp` - Pull left mouth corner up
- `MouthRightPullUp` - Pull right mouth corner up
- `MouthLeftPullSide` - Pull left mouth corner sideways
- `MouthRightPullSide` - Pull right mouth corner sideways
- `MouthLeftPullDown` - Pull left mouth corner down
- `MouthRightPullDown` - Pull right mouth corner down
- `MouthLeftPlatysma` - Activate left platysma muscle
- `MouthRightPlatysma` - Activate right platysma muscle

### Tongue Controls (8)
- `TongueOut` - Stick tongue out
- `TongueUshape` - Form U-shape with tongue
- `TongueUp` - Move tongue up
- `TongueDown` - Move tongue down
- `TongueLeft` - Move tongue left
- `TongueRight` - Move tongue right
- `TonguePointUp` - Point tongue tip up
- `TonguePointDown` - Point tongue tip down

### Special
- `Rest` (Frame 0) - Neutral/rest pose

---

## Technical Details

### BVH Coordinate System

MakeHuman uses:
- **Y-up** coordinate system
- **Right-handed** coordinates
- Rotations in **degrees** (converted internally)

### Performance Considerations

- BVH data is loaded once at startup
- Pose blending happens in real-time
- Each pose unit stores pre-calculated bone transformations
- Blending uses weighted averages of rotation matrices

### Frame Time

The standard `Frame Time: 0.041667` (24 fps) is not used for pose units since they're static poses, not animations. Each "frame" is actually an independent pose snapshot.

### Related Files

**.mhpose** Defines a facial expression by storing small bone pose adjustments used by the Expression Mixer.

**body-poseunits.json** A library of small body pose adjustments, stored as per-bone quaternion rotations, that MakeHuman can blend to build poses.


