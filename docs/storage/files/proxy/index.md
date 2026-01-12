---
title: ".proxy file"
weight: 20
description: "MakeHuman Proxy file"
---

## .proxy file
# MakeHuman Proxy file

## Overview

Proxy files in MakeHuman define alternative geometry meshes that attach to and follow the base human model. They are used for hair, eyes, eyelashes, eyebrows, teeth, tongue, and clothing. The proxy system uses vertex mapping and weight-based interpolation to deform proxy meshes in sync with the base human mesh.

There are three proxy file formats:
- **`.proxy`** - Used for simple proxy meshes (Hair, Eyes, Eyebrows, Eyelashes, Teeth, Tongue, Proxymeshes)
- **`.mhclo`** - Used for clothes and more complex proxy items
- **`.mhpxy`** - Compiled binary format (NPZ) for faster loading (auto-generated from .proxy/.mhclo files)

Both `.proxy` and `.mhclo` files use the same text-based format and are loaded by the same parser. The compiled `.mhpxy` format is automatically generated and cached for performance.

## File Location

- System proxies: `<SystemDataDir>/data/<proxytype>/` (e.g., `data/hair/`, `data/eyes/`)
- User proxies: `<UserDataDir>/data/<proxytype>/`

## Proxy Types

MakeHuman recognizes these proxy types:

| Type | File Extension | Description |
|------|----------------|-------------|
| `Hair` | .proxy | Hair meshes |
| `Eyes` | .proxy | Eye meshes (eyeball geometry) |
| `Eyebrows` | .proxy | Eyebrow meshes |
| `Eyelashes` | .proxy | Eyelash meshes |
| `Teeth` | .proxy | Teeth geometry |
| `Tongue` | .proxy | Tongue geometry |
| `Proxymeshes` | .proxy | Full-body proxy meshes (alternative body geometry) |
| `Clothes` | .mhclo | Clothing items |

## Core Concepts

### Vertex Mapping

Each vertex in the proxy mesh is mapped to 1-3 vertices on the base human mesh using barycentric coordinates:

```
ProxyVertex = w1*HumanVertex1 + w2*HumanVertex2 + w3*HumanVertex3 + Offset
```

Where:
- `w1`, `w2`, `w3` are weights that sum to 1.0
- `Offset` is a 3D displacement vector (x, y, z)

This allows the proxy mesh to deform naturally when the human changes shape, pose, or proportions.

### Exact Fitting vs. Interpolated Fitting

- **Exact fitting**: When a proxy vertex maps to a single human vertex with weight 1.0 (the other two weights are 0.0)
- **Interpolated fitting**: When a proxy vertex is positioned between multiple human vertices using weighted interpolation

### Masking and Deletion

Proxies can specify vertices on the base human mesh that should be hidden when the proxy is active. This prevents Z-fighting and visual artifacts (e.g., hiding the scalp under hair).

## File Format Structure

Proxy files are plain text files with a line-oriented format. Each line contains a keyword followed by parameters.

### Metadata Section

```
# Comment lines start with hash
name <proxy_name>
uuid <unique_identifier>
description <text_description>
author <author_name>
license <license_type>
homepage <url>
tag <tag_name>
version <version_number>
basemesh <basemesh_version>
```

#### Example:
```
name Long Hair
uuid 3f8a9e2d-1234-5678-90ab-cdef01234567
description Beautiful long flowing hair
author John Doe
license CC0
homepage http://example.com
tag hair
tag long
tag female
version 110
basemesh hm08
```

### Geometry References

```
obj_file <relative_path_to_obj>
material <relative_path_to_mhmat>
vertexboneweights_file <relative_path_to_jsonw>
```

The `obj_file` specifies the mesh geometry. MakeHuman will look for:
1. Compiled NPZ version: `filename.npz`
2. Original OBJ version: `filename.obj`

#### Example:
```
obj_file hair_long.obj
material hair_long.mhmat
```

### Render Properties

```
z_depth <integer>
max_pole <integer>
```

- **`z_depth`**: Render order depth (lower values render first). Default: 50
  - Hair typically uses high values (e.g., 100) to render on top
  - Eyes use negative values (e.g., -10) to render behind other geometry
- **`max_pole`**: Maximum number of faces per vertex (topology constraint). Default: 8 (doubled internally to 16)

#### Example:
```
z_depth 100
max_pole 8
```

<!-- 
looks like 'special_pose" has never been used in practice
### Special Poses

```
special_pose <pose_type> <pose_name>
```

Specifies poses that should be applied to the human when this proxy is active. Used to adjust the base mesh to better fit the proxy.

#### Example:
```
special_pose face Smile
special_pose eyebrows_left Raised
```
-->


### Transformation Matrix

The transformation matrix allows the proxy to scale, shear, or transform based on the human's current proportions.

#### Scale Parameters

```
x_scale <vert1> <vert2> <denominator>
y_scale <vert1> <vert2> <denominator>
z_scale <vert1> <vert2> <denominator>
```

Scales the proxy along each axis based on the distance between two vertices on the human mesh:

```
scale[axis] = |human_vertex[vert1][axis] - human_vertex[vert2][axis]| / denominator
```

#### Example:
```
x_scale 1234 5678 0.5
y_scale 2345 6789 0.8
z_scale 3456 7890 0.6
```

#### Shear Parameters

```
shear_x <vert1> <vert2> <x1> <x2>
shear_y <vert1> <vert2> <y1> <y2>
shear_z <vert1> <vert2> <z1> <z2>

l_shear_x <vert1> <vert2> <x1> <x2>
l_shear_y <vert1> <vert2> <y1> <y2>
l_shear_z <vert1> <vert2> <z1> <z2>

r_shear_x <vert1> <vert2> <x1> <x2>
r_shear_y <vert1> <vert2> <y1> <y2>
r_shear_z <vert1> <vert2> <z1> <z2>
```

- **`shear_*`**: General shear transformation
- **`l_shear_*`**: Left-side specific shear
- **`r_shear_*`**: Right-side specific shear

Shear transformations use affine matrix calculation based on vertex positions.

### UV Layer References

```
uvLayer <layer_number> <relative_path_to_mhuv>
```

References UV coordinate files for texture mapping (mostly deprecated in favor of OBJ UV data).

#### Example:
```
uvLayer 0 hair_long.mhuv
```

### Vertex Mapping Section

```
verts
<vertex_index> <weight1> <vertex_index2> <weight2> <vertex_index3> <weight3> [<offset_x> <offset_y> <offset_z>]
...
```

or (for exact fitting):

```
verts
<vertex_index>
...
```

This is the core of the proxy file. Each line after the `verts` keyword defines how one proxy vertex maps to the base human mesh.

#### Exact Fitting Format:
```
verts
1234
5678
9012
```

Each line specifies a single human vertex index. The proxy vertex will be positioned exactly at that human vertex.

#### Interpolated Fitting Format:
```
verts
1234 0.5 5678 0.3 9012 0.2
2345 0.6 6789 0.25 3456 0.15 0.001 0.002 0.0
```

Each line specifies:
- Human vertex indices: `v1`, `v2`, `v3`
- Weights: `w1`, `w2`, `w3` (should sum to approximately 1.0)
- Optional offset: `dx`, `dy`, `dz` (in meters)

The proxy vertex position is calculated as:
```
position = w1 * human[v1] + w2 * human[v2] + w3 * human[v3] + offset
```

**Important**: The `verts` section must contain exactly one line per vertex in the proxy OBJ file, in the same order.

### Weight Groups (Optional)

```
weights <group_name>
<vertex_index> <weight>
...
```

Defines custom vertex groups for weight painting (deprecated in most modern proxies).

### Vertex Deletion Section

```
delete_verts
<vertex_index1> <vertex_index2> ...
<vertex_index_start> - <vertex_index_end>
```

Specifies vertices on the base human mesh that should be hidden when this proxy is active.

#### Example:
```
delete_verts
1234 1235 1236
5000 - 5100
6789
```

This hides vertices 1234, 1235, 1236, all vertices from 5000 to 5100 (inclusive), and vertex 6789.

<!--
This jsonw format does not seem to be used anywhere
though vertexboneweight_file does show up inside some .mhw files

### Custom Vertex Bone Weights

```
vertexboneweights_file <filename.jsonw>
```

References a JSON file containing custom vertex-to-bone weights for skeletal animation. This allows the proxy to have different rigging behavior than the default vertex-mapped weights.

The `.jsonw` file format is defined in the animation module and contains bone names mapped to vertex indices and weights.

-->

## Complete Example

Here's a complete example of a hair proxy file:

```
# Long flowing hair proxy
# Author: Jane Designer
# License: CC-BY

name Long Flowing Hair
uuid a1b2c3d4-e5f6-7890-abcd-ef1234567890
description Beautiful long hair with natural flow
author Jane Designer
license CC-BY 4.0
homepage https://example.com/assets/hair
tag hair
tag long
tag female
tag realistic
version 110
basemesh hm08

obj_file long_hair.obj
material long_hair.mhmat

z_depth 100
max_pole 8

special_pose face Neutral

# Scale with head size
y_scale 411 10358 0.5

# Vertex mapping (simplified example)
verts
411
412
413
8156 0.333 8157 0.333 8158 0.334
8159 0.5 8160 0.5 0.0 0.0 0.001
10358
10359
10360

# Hide scalp vertices under the hair
delete_verts
411 - 450
8000 - 8200
10300 - 10400
```

## Loading Process

1. **Check for compiled binary**: Look for `.mhpxy` file
2. **Validate timestamp**: If ASCII source is newer, recompile
3. **Load from binary** (preferred) or **parse ASCII**
4. **Load OBJ mesh**: Read vertex positions, faces, normals, UVs
5. **Load material**: Apply textures and shader properties
6. **Apply transformation matrix**: Calculate scale/shear based on human proportions
7. **Map vertices**: Calculate proxy vertex positions from human vertex mapping
8. **Apply deletion mask**: Hide specified human vertices
9. **Load custom bone weights** (if specified)

## Compiled Binary Format (.mhpxy)

The compiled NPZ format contains:

| Key | Type | Description |
|-----|------|-------------|
| `name` | String | Proxy name |
| `uuid` | String | Unique identifier |
| `description` | String | Text description |
| `basemesh` | String | Base mesh version |
| `tags_str` | String array | Packed tag strings |
| `tags_idx` | Uint32 array | Tag string indices |
| `lic_str` | String array | License information |
| `lic_idx` | Uint32 array | License indices |
| `obj_file` | String | Path to OBJ file |
| `material_file` | String | Path to material file (optional) |
| `version` | Int32 | Proxy version number |
| `z_depth` | Int32 | Render order depth (optional) |
| `max_pole` | Uint32 | Max pole count (optional) |
| `num_refverts` | Int32 | Number of reference vertices (1 or 3) |
| `ref_vIdxs` | Uint32 array | Human vertex indices (shape: [n,3] or [n]) |
| `weights` | Float32 array | Vertex weights (shape: [n,3] or [n]) |
| `offsets` | Float32 array | Vertex offsets (shape: [n,3], optional) |
| `deleteVerts` | Bool array | Deletion mask (optional) |
| `uvLayers_str` | String array | UV layer paths (optional) |
| `uvLayers_idx` | Uint32 array | UV layer indices (optional) |
| `special_pose_str` | String array | Special pose names (optional) |
| `special_pose_idx` | Uint32 array | Special pose indices (optional) |
| `tmat_scale` | Float32 array | Scale transformation data (optional) |
| `tmat_scale_idx` | Uint32 array | Scale vertex indices (optional) |
| `tmat_shear` | Float32 array | Shear transformation data (optional) |
| `tmat_shear_idx` | Uint32 array | Shear vertex indices (optional) |
| `tmat_lshear` | Float32 array | Left shear data (optional) |
| `tmat_lshear_idx` | Uint32 array | Left shear indices (optional) |
| `tmat_rshear` | Float32 array | Right shear data (optional) |
| `tmat_rshear_idx` | Uint32 array | Right shear indices (optional) |
| `vertexBoneWeights_file` | String | Path to bone weights file (optional) |

The binary format is automatically generated by `compile_proxies.py` and cached for performance.


## Deprecated Parameters

These parameters are no longer used but may appear in old proxy files:

- `backface_culling` - Use material file `backfaceCull` property instead
- `transparent` - Use material file properties instead
- `shapekey` - No longer supported
- `subsurf` - No longer supported
- `shrinkwrap` - No longer supported
- `solidify` - No longer supported
- `objfile_layer` - No longer supported
- `uvtex_layer` - No longer supported
- `use_projection` - No longer supported
- `mask_uv_layer` - No longer supported
- `texture_uv_layer` - No longer supported
- `delete` - No longer supported
- `vertexgroup_file` - Replaced by `vertexboneweights_file`

## Performance Notes

- **Use compiled files**: The `.mhpxy` format loads ~10x faster than ASCII
- **Compact vertex mapping**: Use exact fitting (single vertex reference) when possible
- **Efficient deletion masks**: Minimize the number of deleted vertices
- **Optimize mesh topology**: Keep proxy mesh vertex count reasonable
- **Cache vertex weights**: The proxy system caches weight mapping for performance

## Related 

- [.mhpose file](mhpose.md) - Pose file format
- [.mhw file](mhw.md) - Weight file format
- [face-poseunits](face-posunits.md) - Face pose units
- [body-poseunits](body-poseunits.md) - Body pose units
<!--
- Material file format documentation (see MakeHuman wiki)
- Animation/rigging documentation (see MakeHuman wiki)
-->

## References

- **Source Code**: `makehuman/shared/proxy.py`
- **Compilation Script**: `makehuman/compile_proxies.py`


### Example .proxy file

[female1605.proxy](female1605.proxy)

