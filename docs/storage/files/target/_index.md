---
title: ".target file"
description: "MakeHuman Target file"
---

## .target file
# MakeHuman Target file

## Overview

Target files (`.target`) are morph target files used by MakeHuman to deform the base mesh. They define vertex displacements that modify the shape of the human model. Targets are the fundamental building blocks of MakeHuman's modeling system, controlling everything from gender and age to specific facial features and body proportions.

A target file contains a list of vertex indices and corresponding 3D translation vectors. When a target is applied with a weight (morphFactor), each vertex moves by its translation vector multiplied by that weight.

## File Formats

- **`.target`** - ASCII text format (human-readable, slower to load)
- **`targets.npz`** - Compiled binary NPZ archive (faster loading, auto-generated)
- Individual binary format (deprecated): `.index.npy` and `.vector.npy` per target

The compiled NPZ format contains all targets in a single compressed archive for optimal performance.

## File Location

- System targets: `makehuman/data/targets/`
- Compiled archive: `makehuman/data/targets.npz`
- User targets: `~/makehuman/v1py3/data/targets/`

## Target Naming Convention

Target filenames encode important information about their behavior and dependencies:

```
<category>-<subcategory>-<details>-<variable>-<variable>.target
```

### Filename Components

1. **Path component** (folder name): Base category (e.g., `targets`, `poses`, `expressions`)
2. **Filename parts** (separated by `-` or `_`): Target group keys
3. **Macro variables** (reserved keywords): Dependencies that affect weight calculation

### Reserved Macro Variable Keywords

These keywords in the filename indicate that the target's weight depends on macro modifiers:

| Category | Values | Modifier Group |
| --- | --- | --- |
| `gender` | `male`, `female` | Gender |
| `age` | `baby`, `child`, `young`, `old` | Age |
| `race` | `caucasian`, `asian`, `african` | Ethnicity |
| `muscle` | `maxmuscle`, `averagemuscle`, `minmuscle` | Muscle tone |
| `weight` | `minweight`, `averageweight`, `maxweight` | Weight |
| `height` | `minheight`, `averageheight`, `maxheight` | Height |
| `breastsize` | `mincup`, `averagecup`, `maxcup` | Breast size |
| `breastfirmness` | `minfirmness`, `averagefirmness`, `maxfirmness` | Breast firmness |
| `bodyproportions` | `uncommonproportions`, `regularproportions`, `idealproportions` | Body proportions |

### Naming Examples

```
targets-nose-width-incr.target
  → Group: ['nose', 'width', 'incr']
  → No dependencies

armslegs-r-lowerarm-scale-depth-decr-male.target
  → Group: ['armslegs', 'r', 'lowerarm', 'scale', 'depth', 'decr']
  → Depends on: gender=male

breast-dist-incr-female-young.target
  → Group: ['breast', 'dist', 'incr']
  → Depends on: gender=female, age=young
```

The naming convention allows the system to:
- Organize targets into logical groups
- Automatically calculate weighted combinations based on macro variables
- Support symmetry (left/right) and hierarchical modifications

## ASCII File Format

### Structure

```# Comment lines (license, author, copyright)
# Can include metadata in comments
<vertex_index> <dx> <dy> <dz>
<vertex_index> <dx> <dy> <dz>
...
```

### Format Details

- **Comments**: Lines starting with `#` are comments and can contain license metadata
- **Vertex entries**: Each line contains 4 whitespace-separated values:
  - `vertex_index` (integer): Index of the vertex to modify (0-based)
  - `dx`, `dy`, `dz` (float): Translation vector in meters

## Binary NPZ Archive Format

The compiled `targets.npz` is a compressed ZIP archive containing NumPy arrays:

### Per-Target Files in Archive

For each target file `data/targets/path/to/target.target`, the NPZ contains:

```
data/targets/path/to/target.index.npy    # Vertex indices (uint16)
data/targets/path/to/target.vector.npy   # Translation vectors (int16, scaled by 1000)
data/targets/path/to/target.license.npy  # License metadata (optional)
```
### Data Encoding

- **Indices**: Stored as `uint16` (16-bit unsigned integers)
  - Range: 0-65535 vertices
  - Limitation: Base mesh must have < 65536 vertices

- **Vectors**: Stored as `int16` (16-bit signed integers)
  - Scaled by 1000× (millimeter precision)
  - Range: ±32.767 meters per axis
  - When loading: divided by 1000 to restore meter units
  
## Target Application

### Basic Formula

For each vertex affected by the target:

```
new_position = original_position + (translation_vector × morphFactor × scale)
```

Where:
- `original_position`: Base mesh vertex position
- `translation_vector`: From target file
- `morphFactor`: Weight/strength (typically 0.0 to 1.0, but can be outside this range)
- `scale`: Optional 3D scaling factor (default: [1.0, 1.0, 1.0])

### Weighted Combination

Multiple targets can be applied simultaneously. Their effects are additive:

```
final_position = base_position + Σ(translation_i × weight_i)
```

### Macro-Variable Dependencies

Targets with macro variables in their filenames have their weights automatically calculated based on modifier values:

```python
# Example: breast-dist-incr-female-young.target
# Only applies when gender=female and age=young
effective_weight = base_weight × gender_female_weight × age_young_weight
```

## Target Compilation

### Compiling Targets to NPZ

Run the compilation script:

```bash
cd makehuman
python compile_targets.py
```

This script:
1. Scans `data/` folder for all `.target` files
2. Loads each target as ASCII
3. Converts to binary format (uint16 indices, int16 vectors scaled by 1000)
4. Stores in compressed `data/targets.npz` archive
5. Includes license metadata for each target

### Symmetric Target Handling

Targets can be mirrored for left/right symmetry. The modifier system handles this automatically when symmetry mode is enabled.

## Technical Specifications

### Coordinate System

- **Y-up**: Y-axis points upward
- **Right-handed**: Standard 3D coordinate system
- **Units**: Meters
- **Origin**: Center of base mesh (typically at ground level, pelvis height)

### Precision

- **ASCII format**: Full float precision (6-8 significant digits)
- **Binary format**: Millimeter precision (int16 with 1000× scaling)
  - Precision: 0.001 meters (1mm)
  - Range per axis: ±32.767 meters

## Common Use Cases

### Body Shape Modifiers

```
targets/torso/torso-scale-horiz-incr.target
targets/breast/breast-dist-incr-female.target
targets/legs/legs-height-incr.target
```

### Facial Features

```
targets/nose/nose-width-incr.target
targets/eyes/eyes-height-incr.target
targets/mouth/mouth-width-decr.target
```

### Age and Gender

```
targets/macrodetails/male.target
targets/macrodetails/female.target
targets/macrodetails/young.target
targets/macrodetails/old.target
```

### Expressions

```
expressions/smile.target
expressions/frown.target
expressions/eyebrows-up.target
```

### Poses

```
poses/arm-left-shoulder-rotate.target
poses/hand-right-fist.target
poses/leg-left-knee-bend.target
```

### Example .target file

[upperbody-depth-incr.target](upperbody-depth-incr.target)

