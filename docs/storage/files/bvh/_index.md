---
title: ".bvh file"
weight: 20
description: ""
---

## .bvh file
# BVH File Format in MakeHuman 2

## Overview

BVH (BioVision Hierarchy) is a motion capture file format used in MakeHuman 2 for storing skeletal animations and poses. While originally designed for motion capture data, MakeHuman 2 uses BVH files for both:
- **Single-frame poses** (static poses)
- **Multi-frame animations** (character movements)

## File Format Structure

A BVH file consists of two main sections:

### 1. HIERARCHY Section

Defines the skeleton structure with a tree of joints (bones).

```
HIERARCHY
ROOT <root_bone_name>
{
    OFFSET <x> <y> <z>
    CHANNELS <n> <channel_list>
    JOINT <child_bone_name>
    {
        OFFSET <x> <y> <z>
        CHANNELS <n> <channel_list>
        ...
    }
    End Site
    {
        OFFSET <x> <y> <z>
    }
}
```

**Key Elements:**
- **ROOT**: The top-level bone in the hierarchy (typically the root bone of the skeleton)
- **JOINT**: A bone in the skeleton hierarchy
- **OFFSET**: The relative position of this bone from its parent (x, y, z in units)
- **CHANNELS**: Defines which transformations are animated for this bone
- **End Site**: Marks the endpoint of a bone chain (has offset but no channels)

### 2. MOTION Section

Contains the animation data for all frames.

```
MOTION
Frames: <number_of_frames>
Frame Time: <seconds_per_frame>
<frame_0_data>
<frame_1_data>
...
```

**Key Elements:**
- **Frames**: Total number of animation frames
- **Frame Time**: Duration of each frame in seconds (e.g., 0.041667 = 1/24 second = 24 FPS)
- **Frame Data**: One line per frame containing space-separated values for all channels

## Channel Types

BVH files in MakeHuman 2 support six channel types:

| Channel | Description | Typical Usage |
|---------|-------------|---------------|
| `Xposition` | Translation along X axis | Root bone only |
| `Yposition` | Translation along Y axis | Root bone only |
| `Zposition` | Translation along Z axis | Root bone only |
| `Xrotation` | Rotation around X axis | All bones |
| `Yrotation` | Rotation around Y axis | All bones |
| `Zrotation` | Rotation around Z axis | All bones |

### Channel Order

- **Root bone**: Typically has 6 channels (3 position + 3 rotation)
- **Other bones**: Typically have 3 channels (rotation only)

The order of values in each frame line corresponds to the order channels are declared in the HIERARCHY section.

## Coordinate System

### Import (Loading BVH)

MakeHuman 2 assumes **Z-up coordinate system** by default:
- When `z_up = True` (default):
  - Coordinates are converted: `(x, z, -y)` 
  - Y rotation is negated during import
- When `z_up = False`:
  - Coordinates are used as-is: `(x, y, z)`

### Export (Saving BVH)

When exporting to BVH:
- Uses XYZ rotation order
- Assumes Blender-style coordinate system (Y-forward, Z-up)
- Only root bone can have position changes per frame
- Other bones use rotation only

## Rotation Order

MakeHuman 2 uses **YZX Euler rotation order** internally:
```python
self.rotationorder = "yzx"  # First rotation applied last
```

**Important Notes:**
- The system does not accept changing rotation orders within a file
- When `z_up = True`, rotations are applied in ZYX order (first rotation used last)
- Rotations are stored in degrees in BVH files but converted to radians internally

## Usage in MakeHuman 2

### Loading BVH Files

BVH files are loaded via the **Pose and Animation** module:

1. Navigate to the Poses context in the UI
2. Select a `.bvh` file from the file browser
3. Click the checkmark to apply, or double-click to load and apply


### File Location

BVH files are stored in:
```
data/poses/
```

### Supported Features

#### Single-Frame Poses
- BVH with `Frames: 1` acts as a static pose
- Equivalent to `.mhpose` files for pose data
- Can be created and exported from MakeHuman

#### Multi-Frame Animations
- Full skeletal animations with multiple frames
- Frame rate is configurable (1-70 FPS in UI)
- Supports animation playback with controls:
  - First/Previous/Next/Last frame navigation
  - Play/Pause loop
  - Frame slider for manual selection

#### Animation Player Features
- **Face Animation Toggle**: Enable/disable facial bone animation
- **Overlay Corrections**: Apply pose corrections on top of BVH animation
- **Frame Rate Control**: Adjust playback speed (1-70 FPS)
- **Rotator**: Rotate character during animation playback

### Exporting BVH Files

MakeHuman 2 can export animations to BVH format:

```python
from core.export_bvh import bvhExport

exporter = bvhExport(glob, onground=False, scale=1.0)
exporter.ascSave(baseclass, filename)
```

**Export Options:**
- `onground`: If `True`, adjusts root bone Y position to keep character on ground
- `scale`: Scale factor for offsets and positions

**Export Features:**
- Creates skeleton hierarchy from current skeleton
- Exports animation with pose corrections applied
- Supports different skeleton types (maps bone references)
- Always uses XYZ rotation order for compatibility

## Implementation Details

### Class Structure

**Core Classes:**
- `BVH` (`obj3d/animation.py`): Main BVH loader and data structure
- `BVHJoint` (`obj3d/animation.py`): Represents a single bone/joint in the hierarchy
- `bvhExport` (`core/export_bvh.py`): BVH file exporter

## Integration with Other Formats

### Export to Other Formats

BVH animations can be exported to:
- **glTF** (`.gltf`/`.glb`): With skeleton and animation data
- **Blender** (`.mh2b`): Socket communication format with embedded animation
- **BVH** (`.bvh`): Re-export with corrections and different skeletons

## Limitations

1. **Rotation Order**: Cannot handle varying rotation orders within a file
2. **Channel Consistency**: All frames must have the same channel structure
3. **Bone Dislocation**: Only root bone typically has position channels (except for face animations)
4. **Coordinate Systems**: Must specify `z_up` correctly for proper import

## File Format Comparison

| Feature | BVH | MHPOSE | Advantages |
|---------|-----|--------|------------|
| File Format | ASCII | JSON | BVH: Industry standard, MHPOSE: More flexible |
| Frames | Multiple | Single | BVH: Full animations, MHPOSE: Poses only |
| File Size | Large | Small | MHPOSE better for single poses |
| Compatibility | High | MakeHuman only | BVH works with many tools |
| Edit Ease | Low | High | MHPOSE JSON easier to edit |

## Example BVH File

```
HIERARCHY
ROOT Hips
{
    OFFSET 0.000000 0.960000 0.000000
    CHANNELS 6 Xposition Yposition Zposition Xrotation Yrotation Zrotation
    JOINT Spine
    {
        OFFSET 0.000000 0.100000 0.000000
        CHANNELS 3 Xrotation Yrotation Zrotation
        JOINT Chest
        {
            OFFSET 0.000000 0.150000 0.000000
            CHANNELS 3 Xrotation Yrotation Zrotation
            End Site
            {
                OFFSET 0.000000 0.200000 0.000000
            }
        }
    }
}
MOTION
Frames: 2
Frame Time: 0.041667
0.0 0.96 0.0 0.0 0.0 0.0 5.0 0.0 0.0 10.0 0.0 0.0
0.0 0.96 0.0 0.0 5.0 0.0 10.0 0.0 0.0 15.0 0.0 0.0
```

**Explanation:**
- Skeleton with 3 bones: Hips (root), Spine, Chest
- 2 frames of animation
- 24 FPS (0.041667 seconds per frame)
- Frame data: 6 values (Hips) + 3 values (Spine) + 3 values (Chest) = 12 values per line
