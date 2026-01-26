---
title: ".mhclo file"
description: "MakeHuman2 Clothing file format"
---

## .mhclo file
# MakeHuman Clothing File Format

## Overview

The `.mhclo` file format is used in MakeHuman to define clothing items that attach to and deform with the base human model. These files use the same underlying proxy system as other attached assets (hair, eyes, etc.) but are specifically designed for clothing with additional considerations for layering, fit, and material properties.

The `.mhclo` format is text-based and uses the same parsing system as `.proxy` files. MakeHuman automatically compiles these files into binary `.mhpxy` format for faster loading.

## File Location

- System clothes: `<SystemDataDir>/data/clothes/<basemesh>/` (e.g., `data/clothes/hm08/`)
- User clothes: `<UserDataDir>/data/clothes/<basemesh>/`

Clothing files should be organized in subdirectories based on type (e.g., `shirts/`, `pants/`, `dresses/`, `shoes/`).

## Key Differences from .proxy Files

While `.mhclo` files use the same format as `.proxy` files, they have specific characteristics for clothing:

1. **Z-depth layering**: Clothing requires careful `z_depth` values to ensure proper rendering order (underwear, shirts, jackets, etc.)
2. **Vertex deletion**: More extensive use of `delete_verts` to hide body parts under clothing
3. **Material complexity**: Clothing often has more complex materials with multiple texture layers
4. **Tagging system**: Extensive use of tags for categorization (casual, formal, male, female, etc.)
5. **Fit considerations**: More attention to transformation matrices for fitting different body shapes

## File Format Structure

### Metadata Section

The metadata section defines the clothing item's basic information:

```
name <clothing_name>
uuid <unique_identifier>
description <text_description>
author <author_name>
license <license_type>
homepage <url>
tag <tag_name>
version <version_number>
basemesh <basemesh_version>
```

#### Tags for Clothing

Common tag categories for clothing:

- **Gender**: `male`, `female`, `unisex`
- **Category**: `shirt`, `pants`, `dress`, `shoes`, `hat`, `gloves`, `underwear`
- **Style**: `casual`, `formal`, `sport`, `fantasy`, `historical`
- **Season**: `summer`, `winter`, `spring`, `fall`
- **Layer**: `underwear`, `base`, `mid`, `outer`, `accessories`
- **Fit**: `tight`, `loose`, `fitted`
- **Length**: `short`, `long`, `medium`

#### Example:
```
name Business Suit Jacket
uuid f7e2a19c-4b3d-5e8f-9a1c-2d3e4f567890
description Professional business jacket
author Fashion Designer
license CC-BY 4.0
homepage https://example.com/assets/clothes
tag male
tag formal
tag jacket
tag business
tag outer
version 110
basemesh hm08
```

### Geometry References

```
obj_file <relative_path_to_obj>
material <relative_path_to_mhmat>
vertexboneweights_file <relative_path_to_jsonw>
```

All paths are relative to the `.mhclo` file's directory.

#### Example:
```
obj_file suit_jacket.obj
material suit_jacket.mhmat
```

### Render Properties

```
z_depth <integer>
max_pole <integer>
```

#### Z-depth Guidelines for Clothing

The `z_depth` value determines rendering order. Higher values render on top:

| Layer Type | Z-depth Range | Examples |
|------------|---------------|----------|
| Body (hidden parts) | 0-10 | Parts hidden by underwear |
| Underwear | 10-20 | Underwear, bras, briefs |
| Base clothing | 20-40 | T-shirts, pants, dresses |
| Mid-layer | 40-60 | Sweaters, vests |
| Outer layer | 60-80 | Jackets, coats |
| Footwear | 30-50 | Shoes, boots (varies by style) |
| Accessories | 80-100 | Belts, jewelry, watches |

**Important**: Adjust z_depth based on your specific clothing items to avoid Z-fighting (flickering when two surfaces are at the same depth).

#### Example:
```
z_depth 65
max_pole 8
```

### Transformation Matrix (Scaling)

Clothing often needs to scale with body proportions. The scale parameters ensure clothing fits properly on different body types:

```
x_scale <vert1> <vert2> <denominator>
y_scale <vert1> <vert2> <denominator>
z_scale <vert1> <vert2> <denominator>
```

The scale is calculated as:
```
scale[axis] = |human_vertex[vert1][axis] - human_vertex[vert2][axis]| / denominator
```

#### Common scaling strategies:
- **Full body clothing** (dresses, jumpsuits): Scale with height and width
- **Upper body** (shirts, jackets): Scale with shoulder width and torso height
- **Lower body** (pants, skirts): Scale with hip width and leg length
- **Footwear**: Scale with foot length and width

#### Example:
```
# Scale with shoulder width
x_scale 8274 8275 0.165

# Scale with torso height  
y_scale 7731 4281 0.935

# Scale with body depth
z_scale 4280 4323 0.118
```

### Vertex Mapping Section

```
verts
<mapping_data>
...
```

Each line after `verts` defines how one clothing vertex maps to the base human mesh. The number of lines must exactly match the number of vertices in the OBJ file.

#### Exact Fitting (Single Vertex):
```
verts
7731
7732
7733
```

Each line is a single human vertex index. The clothing vertex will be positioned exactly at that human vertex. Use this for clothing that closely follows the body contour.

#### Interpolated Fitting (Weighted Average):
```
verts
7731 0.5 7732 0.3 7733 0.2
8274 0.4 8275 0.4 8276 0.2 0.002 0.0 0.001
```

Each line contains:
- Three human vertex indices
- Three weights (should sum to ~1.0)
- Optional offset (x, y, z) in meters

The clothing vertex position is calculated as:
```
position = w1*human[v1] + w2*human[v2] + w3*human[v3] + offset
```

Use offsets to:
- Create loose-fitting clothing (positive offsets away from body)
- Add wrinkles and folds
- Adjust fit in specific areas

#### Example with offsets for loose shirt:
```
verts
# Shoulders - exact fit
8274
8275
# Chest - loose fit with offset
7731 0.5 7732 0.5 0.0 0.0 0.015
7733 0.5 7734 0.5 0.0 0.0 0.015
# Torso - medium fit
4281 0.6 4282 0.4 0.0 0.0 0.008
```

### Vertex Deletion Section

The `delete_verts` section specifies which human body vertices should be hidden when the clothing is worn. This prevents the body from showing through the clothing.

```
delete_verts
<vertex_index1> <vertex_index2> ...
<vertex_start> - <vertex_end>
```

#### Strategies for vertex deletion:

1. **Full coverage** (dress, jumpsuit): Delete most of the covered body
2. **Partial coverage** (shirt): Delete torso but not arms/legs
3. **Minimal coverage** (accessories): Delete only immediately covered vertices
4. **Layering consideration**: Lower layers should delete fewer vertices to allow layering

#### Example for a long-sleeve shirt:
```
delete_verts
# Hide torso
7490 - 7850
# Hide upper arms
8100 - 8350
8600 - 8850
# Keep hands, lower arms, and legs visible
```

#### Example for pants:
```
delete_verts
# Hide legs
4280 - 4800
10200 - 10700
# Keep torso, arms, feet visible
```

## Binary Compilation

MakeHuman automatically compiles `.mhclo` files to `.mhpxy` (binary NPZ format) for faster loading:

- Compilation happens automatically on first load
- Binary file is cached for subsequent loads
- If `.mhclo` is modified, binary is regenerated
- Binary files are ~10x faster to load than ASCII

To force recompilation:
- Delete the `.mhpxy` file
- Modify the `.mhclo` file timestamp
- Use the "Rescan" button in MakeHuman
