---
title: "shader.json"
weight: 27
description: "Shader configuration file"
---

## shader.json
# Shader Configuration File

## Overview

The `shader.json` file is the global configuration file for MakeHuman2's rendering system. It defines the default lighting setup, rendering parameters, and visual environment settings that control how the 3D scene is rendered in the viewport.

This file controls:
- Background (clear) color
- Ambient lighting
- Specular lighting properties
- Lighting method (Phong vs Blinn-Phong)
- Skybox settings
- Up to 3 point/directional lights with positions, colors, and intensities

---

## File Location

**System Default:**

```text
<SystemDir>/data/shaders/shader.json
```

**User Override:**

Currently, `shader.json` does not support user overrides. All modifications are made through the MakeHuman2 Scene and Lighting window (Edit → Scene/Lighting), which updates the system file.

---

## File Format

The `shader.json` file uses standard JSON format:

```json
{
    "glclearcolor": [0.2, 0.2, 0.2, 1.0],
    "ambientcolor": [1.0, 1.0, 1.0, 0.35],
    "specularfocus": 30.0,
    "blinn": true,
    "skybox": true,
    "skyboxname": "default",
    "lamps": [
        { "position": [8.5, 8.0, 6.5], "color": [1.0, 1.0, 1.0, 7.0], "type": 0 },
        { "position": [-12.0, 6.0, 6.5], "color": [1.0, 1.0, 1.0, 8.0], "type": 0 },
        { "position": [-0.4, 7.0, -10.0], "color": [1.0, 1.0, 1.0, 5.0], "type": 0 }
    ]
}
```

---

## Configuration Fields

### `glclearcolor`

**Type:** Array of 4 floats [R, G, B, A]  
**Range:** 0.0 to 1.0 per channel  
**Default:** `[0.2, 0.2, 0.2, 1.0]` (dark gray)

The OpenGL clear color, which is the background color of the viewport when no skybox is active.

**Format:**
- Red, Green, Blue, Alpha values in range 0.0-1.0
- RGB values define the color
- Alpha is typically 1.0 (fully opaque)

**Example:**

```json
"glclearcolor": [0.2, 0.2, 0.2, 1.0]
```

**Common Values:**
- `[0.2, 0.2, 0.2, 1.0]` - Dark gray (default, neutral)
- `[0.0, 0.0, 0.0, 1.0]` - Black (for dramatic lighting)
- `[0.5, 0.5, 0.5, 1.0]` - Medium gray
- `[1.0, 1.0, 1.0, 1.0]` - White (for bright, studio-like environment)
- `[0.1, 0.2, 0.3, 1.0]` - Dark blue-gray (atmospheric)

**UI Control:** Edit → Scene/Lighting → Background → Background color

---

### `ambientcolor`

**Type:** Array of 4 floats [R, G, B, Intensity]  
**Range:** 0.0 to 1.0 per channel  
**Default:** `[1.0, 1.0, 1.0, 0.35]`

Defines the ambient light color and intensity. Ambient light provides uniform illumination to all surfaces, preventing completely black shadows.

**Format:**
- First 3 values: RGB color (0.0-1.0)
- Fourth value: Intensity/luminance (0.0-1.0)
- Final ambient contribution = RGB × Intensity

**Example:**

```json
"ambientcolor": [1.0, 1.0, 1.0, 0.35]
```

This creates neutral white ambient light at 35% intensity.

**Common Values:**
- `[1.0, 1.0, 1.0, 0.35]` - Neutral white ambient (default)
- `[1.0, 1.0, 1.0, 0.25]` - Darker, more dramatic
- `[1.0, 1.0, 1.0, 0.50]` - Brighter, softer shadows
- `[1.0, 0.95, 0.90, 0.35]` - Warm tinted ambient
- `[0.90, 0.95, 1.0, 0.35]` - Cool tinted ambient

**UI Control:** Edit → Scene/Lighting → Ambient Light → Luminance and Color

---

### `specularfocus`

**Type:** Float  
**Range:** 1.0 to 64.0  
**Default:** `30.0`

Controls the sharpness/size of specular highlights (shiny spots on surfaces). Higher values create smaller, sharper highlights; lower values create larger, softer highlights.

This value is the "shininess" exponent in the Phong/Blinn-Phong lighting model.

**Example:**

```json
"specularfocus": 30.0
```

**Common Values:**
- `1.0-5.0` - Very soft, broad highlights (rubber, matte plastic)
- `10.0-20.0` - Moderate highlights (painted surfaces, semi-gloss)
- `30.0` - Default (balanced for skin and general materials)
- `40.0-64.0` - Sharp, tight highlights (glossy surfaces, wet skin, polished metal)

**Effect:**
- Lower = Broader, softer specular highlights
- Higher = Tighter, sharper specular highlights

**UI Control:** Edit → Scene/Lighting → Phong specific → Specular light, Focus

---

### `blinn`

**Type:** Boolean  
**Default:** `true`

Selects between Blinn-Phong and standard Phong specular lighting calculation.

**Values:**
- `true` - Use Blinn-Phong (more physically accurate, recommended)
- `false` - Use standard Phong (legacy, may look different)

**Difference:**

- **Phong:** Uses reflection vector
  - `specular = pow(max(dot(reflect_dir, view_dir), 0.0), shininess)`
  
- **Blinn-Phong:** Uses halfway vector
  - `specular = pow(max(dot(normal, halfway), 0.0), shininess)`

Blinn-Phong generally produces more consistent and realistic highlights, especially at grazing angles.

**Example:**

```json
"blinn": true
```

**UI Control:** Edit → Scene/Lighting → Phong specific → Blinn/Phong radio buttons

---

### `skybox`

**Type:** Boolean  
**Default:** `true`

Enables or disables the skybox (environment cubemap).

**Values:**
- `true` - Skybox is rendered as background and provides environment reflections
- `false` - Skybox disabled, `glclearcolor` is used as background

**Example:**

```json
"skybox": true
```

**Effect:**
- When `true`: 360° environment background, provides reflections for PBR materials
- When `false`: Solid color background (from `glclearcolor`)

**UI Control:** Edit → Scene/Lighting → Background → Skybox checkbox

---

### `skyboxname`

**Type:** String  
**Default:** `"default"`

Specifies which skybox to use when skybox rendering is enabled.

**Format:**
- Name of a directory in `data/shaders/skybox/`
- Directory must contain 6 cubemap face images (px, nx, py, ny, pz, nz)

**Example:**

```json
"skyboxname": "default"
```

**Available Skyboxes:**

Skyboxes are stored in `data/shaders/skybox/<name>/` with files:
- `posx.jpg` or `posx.png` - Positive X (right)
- `negx.jpg` or `negx.png` - Negative X (left)
- `posy.jpg` or `posy.png` - Positive Y (up)
- `negy.jpg` or `negy.png` - Negative Y (down)
- `posz.jpg` or `posz.png` - Positive Z (front)
- `negz.jpg` or `negz.png` - Negative Z (back)

**Notes:**
- Files can be either `.jpg` or `.png` format
- All 6 faces must be present for a valid skybox
- Images should be square (typically 2048×2048 pixels)

**Common Skyboxes:**
- `"default"` - Standard skybox (if available)
- Custom skybox names based on available directories

**UI Control:** Edit → Scene/Lighting → Background → Skybox list and Select button

---

### `lamps`

**Type:** Array of light objects  
**Maximum:** 3 lights  
**Default:** 3 point lights at different positions

Defines the scene's light sources. MakeHuman2 supports up to 3 simultaneous lights, each with configurable position, color, intensity, and type.

**Array Structure:**

```json
"lamps": [
    {
        "position": [8.5, 8.0, 6.5],
        "color": [1.0, 1.0, 1.0, 7.0],
        "type": 0
    },
    {
        "position": [-12.0, 6.0, 6.5],
        "color": [1.0, 1.0, 1.0, 8.0],
        "type": 0
    },
    {
        "position": [-0.4, 7.0, -10.0],
        "color": [1.0, 1.0, 1.0, 5.0],
        "type": 0
    }
]
```

#### Light Object Properties

##### `position`

**Type:** Array of 3 floats [X, Y, Z]  
**Meaning:** World-space coordinates or direction vector

For **point lights (type 0):**
- Position in 3D space where the light is located
- Light radiates from this point in all directions
- Distance affects attenuation

For **directional lights (type 1):**
- Direction vector (not position)
- Simulates infinitely distant light source
- No distance attenuation (like sunlight)

**Example:**

```json
"position": [8.5, 8.0, 6.5]
```

This places a light at X=8.5, Y=8.0 (height), Z=6.5.

##### `color`

**Type:** Array of 4 floats [R, G, B, Intensity]  
**Range:** 0.0-1.0 for RGB, variable for intensity

Defines the light's color and brightness.

**Format:**
- First 3 values: RGB color (0.0-1.0)
- Fourth value: Intensity/range
  - For point lights: affects attenuation distance
  - For directional lights: affects brightness
  - Typical range: 1.0-10.0

**Example:**

```json
"color": [1.0, 1.0, 1.0, 7.0]
```

This creates a white light with intensity 7.0.

**Common Colors:**
- `[1.0, 1.0, 1.0, 7.0]` - Neutral white
- `[1.0, 0.95, 0.85, 7.0]` - Warm white (tungsten)
- `[0.85, 0.90, 1.0, 7.0]` - Cool white (daylight)
- `[1.0, 0.80, 0.60, 5.0]` - Orange (sunset)
- `[0.95, 0.85, 0.70, 6.0]` - Warm studio light

##### `type`

**Type:** Integer (0 or 1)  
**Values:**
- `0` - Point light (omnidirectional, position-based)
- `1` - Directional light (parallel rays, direction-based)

**Example:**

```json
"type": 0
```

**Point Light (type 0):**
- Light radiates from a specific point
- Intensity decreases with distance (inverse square law)
- Good for: lamps, bulbs, candles, local illumination
- Attenuation: `intensity / (distance * distance)`

**Directional Light (type 1):**
- Light rays are parallel
- No distance attenuation
- Position vector treated as direction
- Good for: sun, moon, distant light sources
- Attenuation: `intensity / 4.0` (constant)

**UI Control:** Edit → Scene/Lighting → Light Source panels (Luminance, Color, Position controls)

---

## Default Configuration

The default `shader.json` provides a three-light setup suitable for character modeling:

```json
{
    "glclearcolor": [0.2, 0.2, 0.2, 1.0],
    "ambientcolor": [1.0, 1.0, 1.0, 0.35],
    "specularfocus": 30.0,
    "blinn": true,
    "skybox": true,
    "skyboxname": "default",
    "lamps": [
        { "position": [8.5, 8.0, 6.5], "color": [1.0, 1.0, 1.0, 7.0], "type": 0 },
        { "position": [-12.0, 6.0, 6.5], "color": [1.0, 1.0, 1.0, 8.0], "type": 0 },
        { "position": [-0.4, 7.0, -10.0], "color": [1.0, 1.0, 1.0, 5.0], "type": 0 }
    ]
}
```

**Lighting Setup:**
- **Light 1:** Right-front key light (intensity 7.0)
- **Light 2:** Left-front fill light (intensity 8.0)
- **Light 3:** Back rim light (intensity 5.0)
- 35% ambient light for shadow fill

This creates a balanced three-point lighting setup typical for character work.

---

## Modifying the Configuration

### Through the Scene and Lighting Window

The primary way to modify shader settings is through the UI:

1. **Open Scene/Lighting:** Edit → Scene/Lighting
2. **Adjust Settings:**
   - **Ambient Light:** Luminance slider and color picker
   - **Phong Specific:** Specular focus slider, Blinn/Phong radio buttons
   - **Background:** Color picker, skybox checkbox and selection
   - **Light Sources:** Three expandable panels with controls for each light
     - Luminance (intensity) slider
     - Color picker
     - Position controls (X/Z map, Y height slider)
     - Point/Directional toggle

3. **Save:** Changes are automatically applied to the viewport and saved to `shader.json`

---

## Common Lighting Scenarios

### Studio Portrait Lighting

Bright, even lighting for detailed character work:

```json
{
    "glclearcolor": [0.3, 0.3, 0.3, 1.0],
    "ambientcolor": [1.0, 1.0, 1.0, 0.45],
    "specularfocus": 32.0,
    "blinn": true,
    "skybox": false,
    "skyboxname": "default",
    "lamps": [
        { "position": [6.0, 8.0, 8.0], "color": [1.0, 1.0, 1.0, 10.0], "type": 0 },
        { "position": [-6.0, 8.0, 8.0], "color": [1.0, 1.0, 1.0, 10.0], "type": 0 },
        { "position": [0.0, 10.0, -8.0], "color": [1.0, 1.0, 1.0, 6.0], "type": 0 }
    ]
}
```

**Characteristics:**
- Higher ambient light (45%)
- Bright, symmetrical key lights
- Moderate backlight
- Neutral gray background
- Good for detailed inspection

### Dramatic Lighting

Low ambient, strong directional light for mood:

```json
{
    "glclearcolor": [0.05, 0.05, 0.08, 1.0],
    "ambientcolor": [0.9, 0.95, 1.0, 0.15],
    "specularfocus": 40.0,
    "blinn": true,
    "skybox": true,
    "skyboxname": "default",
    "lamps": [
        { "position": [1.0, 0.8, 0.5], "color": [1.0, 0.95, 0.85, 8.0], "type": 1 },
        { "position": [-0.5, 0.3, -0.8], "color": [0.6, 0.7, 1.0, 3.0], "type": 1 },
        { "position": [0.0, 8.0, 0.0], "color": [1.0, 1.0, 1.0, 0.5], "type": 0 }
    ]
}
```

**Characteristics:**
- Low ambient light (15%)
- Directional sun-like key light (warm)
- Cool fill light
- Minimal top light
- Dark background
- Strong shadows, high contrast

### Outdoor/Natural Lighting

Simulates sunlight and sky:

```json
{
    "glclearcolor": [0.5, 0.6, 0.8, 1.0],
    "ambientcolor": [0.85, 0.90, 1.0, 0.40],
    "specularfocus": 28.0,
    "blinn": true,
    "skybox": true,
    "skyboxname": "default",
    "lamps": [
        { "position": [0.5, 1.0, 0.3], "color": [1.0, 0.98, 0.95, 9.0], "type": 1 },
        { "position": [-0.3, 0.4, -0.6], "color": [0.7, 0.8, 1.0, 2.0], "type": 1 },
        { "position": [0.0, 8.0, 0.0], "color": [1.0, 1.0, 1.0, 0.0], "type": 0 }
    ]
}
```

**Characteristics:**
- Directional sun (warm, type 1)
- Cool sky fill light (blue tint)
- Light blue-gray background
- Cool-tinted ambient
- Third light disabled (intensity 0)

### Warm Interior Lighting

Indoor tungsten/warm lighting:

```json
{
    "glclearcolor": [0.15, 0.12, 0.10, 1.0],
    "ambientcolor": [1.0, 0.90, 0.75, 0.30],
    "specularfocus": 25.0,
    "blinn": true,
    "skybox": false,
    "skyboxname": "default",
    "lamps": [
        { "position": [8.0, 9.0, 5.0], "color": [1.0, 0.85, 0.65, 8.0], "type": 0 },
        { "position": [-8.0, 7.0, 5.0], "color": [1.0, 0.90, 0.70, 6.0], "type": 0 },
        { "position": [0.0, 6.0, -8.0], "color": [1.0, 0.88, 0.68, 4.0], "type": 0 }
    ]
}
```

**Characteristics:**
- Warm orange/yellow color temperature
- Warm-tinted ambient
- Point lights for natural falloff
- Dark warm-tinted background
- Cozy interior feel

---

## Integration with Shaders

The `shader.json` configuration is read during initialization and passed as uniform variables to the fragment shaders.

### Loading Process

1. **Initialization:** `core/globenv.py` reads `shader.json` via `readShaderInitJSON()`
2. **Storage:** Settings stored in `glob.shaderInit`
3. **Light Setup:** `opengl/camera.py` creates `Light` object with these settings
4. **Shader Uniforms:** Values passed to shaders via `setShader()` method

### Shader Uniform Mapping

The configuration values are passed to GLSL shaders as uniforms:

| JSON Field | GLSL Uniform | Type | Shaders |
| --- | --- | --- | --- |
| `ambientcolor` | `ambientLight` | `vec4` | phong, pbr, toon |
| `specularfocus` | `lightWeight.y` | `float` | phong |
| `blinn` | `blinn` | `bool` | phong |
| `lamps[i].position` | `pointLights[i].position` | `vec3` | phong, pbr, toon |
| `lamps[i].color` | `pointLights[i].color` | `vec3` | phong, pbr, toon |
| `lamps[i].color[3]` | `pointLights[i].intensity` | `float` | phong, pbr, toon |
| `lamps[i].type` | `pointLights[i].type` | `int` | phong, pbr, toon |
| `skybox` | `useSky` | `bool` | pbr |
| `glclearcolor` | Used for `glClearColor()` | - | OpenGL |

### Affected Shaders

The configuration affects these shaders:
- **`phong.frag`** - All lighting settings, specular focus, Blinn toggle
- **`pbr.frag`** - Lighting, ambient, skybox reflections
- **`toon.frag`** - Lighting for cel shading
- **`litsphere.frag`** - Minimal (mostly uses lit-sphere texture)
---

## Example Configurations

### Minimal Lighting

Single directional light for fastest rendering:

```json
{
    "glclearcolor": [0.8, 0.8, 0.8, 1.0],
    "ambientcolor": [1.0, 1.0, 1.0, 0.60],
    "specularfocus": 30.0,
    "blinn": true,
    "skybox": false,
    "skyboxname": "default",
    "lamps": [
        { "position": [0.5, 1.0, 0.5], "color": [1.0, 1.0, 1.0, 6.0], "type": 1 },
        { "position": [0.0, 0.0, 0.0], "color": [1.0, 1.0, 1.0, 0.0], "type": 0 },
        { "position": [0.0, 0.0, 0.0], "color": [1.0, 1.0, 1.0, 0.0], "type": 0 }
    ]
}
```

### High-Contrast Fashion Lighting

Strong key, minimal fill:

```json
{
    "glclearcolor": [0.0, 0.0, 0.0, 1.0],
    "ambientcolor": [1.0, 1.0, 1.0, 0.10],
    "specularfocus": 45.0,
    "blinn": true,
    "skybox": false,
    "skyboxname": "default",
    "lamps": [
        { "position": [10.0, 10.0, 8.0], "color": [1.0, 1.0, 1.0, 12.0], "type": 0 },
        { "position": [-4.0, 6.0, 8.0], "color": [1.0, 1.0, 1.0, 3.0], "type": 0 },
        { "position": [0.0, 8.0, -12.0], "color": [1.0, 1.0, 1.0, 8.0], "type": 0 }
    ]
}
```

### Soft Portrait Lighting

Even, flattering lighting for portraits:

```json
{
    "glclearcolor": [0.95, 0.95, 0.95, 1.0],
    "ambientcolor": [1.0, 0.98, 0.95, 0.50],
    "specularfocus": 22.0,
    "blinn": true,
    "skybox": false,
    "skyboxname": "default",
    "lamps": [
        { "position": [5.0, 8.0, 10.0], "color": [1.0, 0.98, 0.95, 9.0], "type": 0 },
        { "position": [-5.0, 8.0, 10.0], "color": [1.0, 0.98, 0.95, 9.0], "type": 0 },
        { "position": [0.0, 12.0, 0.0], "color": [1.0, 1.0, 1.0, 5.0], "type": 0 }
    ]
}
```

---

## Summary

The `shader.json` file is the central configuration for MakeHuman2's lighting and rendering environment. It provides control over:

- Background color and skybox
- Ambient lighting
- Specular highlights
- Up to 3 configurable lights (point or directional)
- Lighting calculation method (Phong/Blinn-Phong)

**Key Points:**
- Modified through UI (Edit → Scene/Lighting) or manual editing
- Changes immediately affect viewport rendering
- Supports both point lights (positional) and directional lights (sun-like)
- Lighting settings passed to fragment shaders as uniforms
- Critical for achieving desired mood and visual quality


### Sample shader.json file

[shader.json](shader.json)

