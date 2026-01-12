---
title: ".mhmat file"
weight: 20
description: "MakeHuman Materials file"
---

## .mhmat file
# MakeHuman Materials file

## Overview

Material files (`.mhmat`) define the visual appearance of 3D objects in MakeHuman. They specify colors, textures, shading properties, and shader configurations that control how objects are rendered. Materials are used for skin, eyes, hair, clothing, and all other visible geometry.

The `.mhmat` format is a line-oriented text format that is easy to read and edit. Each material file defines properties like diffuse/specular colors, texture maps, transparency, shader programs, and rendering flags.

## An .mhmat file typically specifies:

- Diffuse (base color) textures

- Normal maps

- Specular / roughness / gloss parameters

- Opacity or transparency settings

- Shader-related options

- References to texture image files

## File Location

- System materials: `<SystemDataPath>/data/skins/`, `makehuman/data/materials/`, `makehuman/data/eyes/materials/`, etc.
- User materials: `<UserDataPath>/data/skins/`, `<UserDataPath>/data/materials/`
- Proxy materials: Specified in `.proxy` or `.mhclo` files via `material` keyword

## File Structure

Material files are plain text with a simple keyword-value format:

```
# Comments start with # or //
keyword value1 value2 ...
```

## Basic Properties

### Name and Description

```
name <material_name>
description <text_description>
tag <tag_name>
```

- **`name`**: Unique identifier for this material
- **`description`**: Human-readable description
- **`tag`**: Classification tags (can have multiple)

## Color Properties

### Color Format

Colors are specified as RGB values in the range 0.0 to 1.0:

```
<colorProperty> <red> <green> <blue>
```

### Ambient Color

```
ambientColor <r> <g> <b>
```

Ambient light reflection color. Affects how the material appears in ambient lighting.

**Default**: `0.0 0.0 0.0` (black)

#### Example

```
ambientColor 0.11 0.11 0.11
```

### Diffuse Color

```
diffuseColor <r> <g> <b>
```

Base color of the material. The primary color you see under direct lighting.

**Default**: `1.0 1.0 1.0` (white)

#### Example

```
diffuseColor 1.0 0.8 0.7
```

### Specular Color

```
specularColor <r> <g> <b>
```

Color of specular highlights (shiny reflections).

**Default**: `1.0 1.0 1.0` (white)

#### Example

```
specularColor 0.3 0.3 0.3
```

### Emissive Color

```
emissiveColor <r> <g> <b>
```

Self-illumination color. Makes the material appear to glow (adds color regardless of lighting).

**Default**: `0.0 0.0 0.0` (no emission)

#### Example

```
emissiveColor 0.1 0.0 0.0
```

### Viewport Color and Alpha

```
viewPortColor <r> <g> <b>
viewPortAlpha <alpha>
```

Optional custom color for viewport display (wireframe/flat shading modes).

**Default**: Derived from other color properties

#### Example

```
viewPortColor 0.8 0.6 0.5
viewPortAlpha 0.9
```

## Material Properties

### Shininess

```
shininess <value>
```

Specular shininess/hardness. Controls the size and intensity of specular highlights.

- **Range**: 0.0 (dull) to 1.0 (very shiny)
- **Default**: 0.2

#### Example

```
shininess 0.96
```

### Opacity

```
opacity <value>
```

Material opacity/alpha. Controls overall transparency.

- **Range**: 0.0 (fully transparent) to 1.0 (fully opaque)
- **Default**: 1.0

**Note**: Set `transparent true` to enable transparency rendering when opacity < 1.0.

#### Example

```
opacity 0.8
```

### Translucency

```
translucency <value>
```

Translucency/sub-surface scattering amount. Controls light passing through the material.

- **Range**: 0.0 (opaque) to 1.0 (highly translucent)
- **Default**: 0.0

#### Example

```
translucency 0.3
```

## Rendering Flags

All rendering flags accept boolean values: `true`, `false`, `yes`, `no`, `enabled`, `disabled`

### Shadeless

```
shadeless <boolean>
```

Disables lighting calculations. Material uses only vertex colors and textures without shading.

**Default**: `false`

#### Example

```
shadeless false
```

### Wireframe

```
wireframe <boolean>
```

Renders object in wireframe mode.

**Default**: `false`

#### Example

```
wireframe false
```

### Transparent

```
transparent <boolean>
```

Enables transparency rendering. Required when using opacity < 1.0 or transparency maps.

**Default**: `false`

#### Example

```
transparent true
```

### Alpha to Coverage

```
alphaToCoverage <boolean>
```

Enables alpha-to-coverage (A2C) rendering when GPU supports it. Improves quality of transparent edges but disables anti-aliasing for this object.

**Default**: `true`

**Note**: Only applies when `transparent true` is set.

#### Example

```
alphaToCoverage true
```

### Backface Culling

```
backfaceCull <boolean>
```

Enables backface culling (hides back faces of polygons). Set to `false` for double-sided materials.

**Default**: `true`

#### Example

```
backfaceCull false
```

### Depthless

```
depthless <boolean>
```

Disables depth testing. Object is not occluded and does not occlude others (always visible).

**Default**: `false`

#### Example

```
depthless false
```

### Cast Shadows

```
castShadows <boolean>
```

Enables shadow casting. Object casts shadows on other objects.

**Default**: `true`

#### Example

```
castShadows true
```

### Receive Shadows

```
receiveShadows <boolean>
```

Enables shadow receiving. Object receives shadows from other objects.

**Default**: `true`

#### Example

```
receiveShadows true
```

## Texture Maps

All texture paths are relative to the material file location or absolute paths.

### Diffuse Texture

```
diffuseTexture <path_to_texture>
```

Main color/albedo texture map.

**Supported formats**: PNG, JPG, TGA, BMP

#### Example

```
diffuseTexture diffuse.png
diffuseTexture ../textures/skin_diffuse.png
```

### Bump Map Texture

```
bumpmapTexture <path_to_texture>
bumpmapIntensity <value>
```

Bump map for surface detail (grayscale height map).

- **`bumpmapIntensity`**: Range 0.0 to 1.0, default 1.0

#### Example

```
bumpmapTexture bump.png
bumpmapIntensity 0.8
```

### Normal Map Texture

```
normalmapTexture <path_to_texture>
normalmapIntensity <value>
```

Normal map for surface detail (RGB-encoded normal vectors).

- **`normalmapIntensity`**: Range 0.0 to 1.0, default 1.0

**Note**: Normal maps take precedence over bump maps if both are specified.

#### Example

```
normalmapTexture normal.png
normalmapIntensity 1.0
```

### Displacement Map Texture

```
displacementmapTexture <path_to_texture>
displacementmapIntensity <value>
```

Displacement map for geometric detail (requires shader support).

- **`displacementmapIntensity`**: Range 0.0 to 1.0, default 1.0

#### Example

```
displacementmapTexture displacement.png
displacementmapIntensity 0.5
```

### Specular Map Texture

```
specularmapTexture <path_to_texture>
specularmapIntensity <value>
```

Specular/glossiness map (controls shininess per-pixel).

- **`specularmapIntensity`**: Range 0.0 to 1.0, default 1.0

#### Example

```
specularmapTexture specular.png
specularmapIntensity 0.9
```

### Transparency Map Texture

```
transparencymapTexture <path_to_texture>
transparencymapIntensity <value>
```

Transparency/alpha map (grayscale controls opacity per-pixel).

- **`transparencymapIntensity`**: Range 0.0 to 1.0, default 1.0

**Note**: Requires `transparent true` to be set.

#### Example

```
transparencymapTexture alpha.png
transparencymapIntensity 1.0
```

### Ambient Occlusion Map Texture

```
aomapTexture <path_to_texture>
aomapIntensity <value>
```

Ambient occlusion map (darkens crevices and contact areas).

- **`aomapIntensity`**: Range 0.0 to 1.0, default 1.0

#### Example

```
aomapTexture ao.png
aomapIntensity 0.7
```

## Sub-Surface Scattering (SSS)

Sub-surface scattering simulates light penetrating and scattering inside translucent materials (like skin).

```
sssEnabled <boolean>
sssRScale <value>
sssGScale <value>
sssBScale <value>
```

- **`sssEnabled`**: Enable/disable SSS
- **`sssRScale`**: Red channel scattering scale (depth)
- **`sssGScale`**: Green channel scattering scale
- **`sssBScale`**: Blue channel scattering scale

Higher values = deeper light penetration.

#### Example

```
sssEnabled true
sssRScale 4.0
sssGScale 2.0
sssBScale 1.0
```

## Skin Blending

```
autoBlendSkin <boolean>
```

Automatically adapts diffuse color and litsphere texture to match the character's skin tone (based on ethnicity modifiers).

**Default**: `false`

**Use case**: Default skin materials that should adapt to different ethnicities.

#### Example

```
autoBlendSkin true
```

## UV Mapping

```
uvMap <path_to_mhuv>
```

Custom UV coordinate mapping file.

**Default**: Uses mesh's default UV coordinates

**Note**: `data/uvs/default.mhuv` is a meta-file that refers to the default UV set.

#### Example

```
uvMap custom_uvmap.mhuv
```

## Shader Configuration

### Shader Program

```
shader <path_to_shader>
```

Specifies the GLSL shader program to use for rendering this material.

**Common shaders**:
- `data/shaders/glsl/litsphere` - Lit-sphere shading (default for skin)
- `data/shaders/glsl/phong` - Phong shading
- `data/shaders/glsl/toon` - Toon/cel shading
- `data/shaders/glsl/xray` - X-ray visualization

#### Example

```
shader data/shaders/glsl/litsphere
```

### Shader Parameters

```
shaderParam <parameter_name> <value>
```

Sets custom uniform parameters for the shader program. The parameter type and meaning depends on the shader.

**Common parameters**:
- `litsphereTexture <path>` - Lit-sphere texture for litsphere shader
- `AdditiveShading <float>` - Additive shading amount
- Custom parameters defined in shader code

#### Example

```
shaderParam litsphereTexture data/litspheres/skinmat.png
shaderParam AdditiveShading 0.5
shaderParam customValue 1.0 0.5 0.3
```

### Shader Defines

```
shaderDefine <define_name>
```

Adds preprocessor defines to the shader code. Used for conditional compilation of shader features.

**Note**: Most common defines are set automatically via `shaderConfig`. Custom defines are for advanced use.

#### Example

```
shaderDefine CUSTOM_FEATURE
shaderDefine USE_ADVANCED_LIGHTING
```

### Shader Configuration Options

```
shaderConfig <option> <boolean>
```

Configures built-in shader features. These automatically set appropriate shader defines and parameters.

| Option | Description |
| --- | --- |
| `diffuse` | Enable diffuse texture mapping |
| `bump` | Enable bump mapping |
| `normal` | Enable normal mapping |
| `displacement` | Enable displacement mapping |
| `spec` | Enable specular mapping |
| `vertexColors` | Enable vertex color support |
| `transparency` | Enable transparency mapping |
| `ambientOcclusion` | Enable ambient occlusion mapping |

**Default**: All enabled (if corresponding textures are specified)

#### Example

```
shaderConfig diffuse true
shaderConfig bump true
shaderConfig normal false
shaderConfig displacement false
shaderConfig spec true
shaderConfig vertexColors false
shaderConfig transparency true
shaderConfig ambientOcclusion true
```


## Deprecated Parameters

These parameters are deprecated and should not be used in new materials:

- **`diffuseIntensity`** - Use color values directly
- **`specularIntensity`** - Use color values directly

## References

- **Source Code**: `makehuman/shared/material.py`
- **Material Editor Plugin**: `makehuman/plugins/7_material_editor.py`


### Example .proxy file

[female1605.proxy](female1605.proxy)

