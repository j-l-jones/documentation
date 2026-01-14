---
title: ".frag file"
weight: 26
description: "Fragment shaders"
---

## .frag file
# Fragment Shaders in MakeHuman 2

## Overview

Fragment shaders (`.frag` files) are GLSL (OpenGL Shading Language) programs that determine the final color of each pixel rendered on screen. In MakeHuman 2, fragment shaders work alongside vertex shaders (`.vert`) to create different visual styles for characters, from realistic PBR (Physically Based Rendering) to stylized toon shading.

Fragment shaders are responsible for:
- Calculating lighting and shading (diffuse, specular, ambient)
- Applying textures and texture mapping
- Implementing material properties (roughness, metallic, etc.)
- Creating special effects (x-ray, toon outlines, etc.)
- Handling transparency and alpha blending

## GLSL Version

MakeHuman 2 uses **GLSL version 330** (OpenGL 3.3) as the standard, with fallback to **version 120** (OpenGL 1.2) for macOS compatibility.

**Version directive:**
```glsl
#version 330
```

or for legacy macOS:
```glsl
#version 120
```

## File Location

Fragment shaders are stored in the shaders directory:

```
<SystemDir>/data/shaders/
```

### Naming Convention

Shaders come in pairs with matching base names:
- `<shadername>.vert` - Vertex shader
- `<shadername>.frag` - Fragment shader
- Optional: `<shadername>.geom` - Geometry shader

**macOS prefix:** On macOS, shader files use the `v12_` prefix for OpenGL 1.2 compatibility:
- `v12_pbr.frag`
- `v12_phong.frag`
- etc.

## Available Shaders in MakeHuman 2

### 1. Phong Shader (`phong.frag`)

Classic Phong lighting model with ambient, diffuse, and specular components.

**Features:**
- Traditional Phong or Blinn-Phong specular reflection
- Up to 3 point lights or directional lights
- Ambient occlusion support
- Basic texture mapping

**Use cases:**
- General-purpose shading
- Fast performance
- Compatibility with older systems

### 2. PBR Shader (`pbr.frag`)

Physically Based Rendering using the Cook-Torrance microfacet model.

**Features:**
- Metallic-roughness workflow
- Trowbridge-Reitz GGX normal distribution
- Fresnel-Schlick approximation
- Image-based lighting (IBL) with environment maps
- Normal mapping
- Emissive maps
- Ambient occlusion
- Skybox reflections for metallic surfaces

**Use cases:**
- Realistic materials
- Metal, plastic, leather, etc.
- Professional-quality renders

### 3. Toon Shader (`toon.frag`)

Cel-shading/cartoon-style rendering with sharp lighting transitions.

**Features:**
- Silhouette detection
- Stepped diffuse shading
- Stepped specular highlights
- Customizable thresholds

**Use cases:**
- Cartoon/anime characters
- Stylized art
- Comic book appearance

### 4. Litsphere Shader (`litsphere.frag`)

Image-based lighting using a lit sphere (MatCap) texture.

**Features:**
- Fast, pre-computed lighting
- No real-time light calculations
- Additive shading mode
- Screen-space normal mapping

**Use cases:**
- Quick previews
- Sculpting/modeling reference
- Consistent lighting regardless of scene

### 5. X-Ray Shader (`xray.frag`)

Transparency effect based on viewing angle.

**Features:**
- Edge falloff
- Adjustable intensity
- Fresnel-style transparency
- Good for visualizing topology

**Use cases:**
- Mesh topology inspection
- Weight painting visualization
- Technical viewing

### 6. Fixed Color Shader (`fixcolor.frag`)

Simple solid color rendering without texture or lighting.

**Features:**
- Flat vertex colors
- Transparency support
- No lighting calculations

**Use cases:**
- Debug visualization
- Simple colored primitives
- UI elements

### 7. Skybox Shader (`skybox.frag`)

Environment cubemap rendering.

**Features:**
- Cubemap texture sampling
- 360-degree environment
- Background rendering

**Use cases:**
- Scene environment
- Reflections source
- Backdrop

## Fragment Shader Structure

### Basic Template

```glsl
#version 330

// Output
out vec4 FragColor;

// Input from vertex shader
in VS_OUT {
    vec3 FragPos;    // Fragment position in world space
    vec3 Normal;     // Interpolated normal
    vec2 TexCoords;  // Texture coordinates
} fs_in;

// Uniforms (constants from application)
uniform sampler2D Texture;
uniform vec3 viewPos;
uniform vec4 ambientLight;

void main()
{
    // Calculate final color
    vec3 color = texture(Texture, fs_in.TexCoords).rgb;
    
    // Your lighting calculations here
    
    FragColor = vec4(color, 1.0);
}
```

### Common Input Variables

#### From Vertex Shader

Data passed from the vertex shader (interpolated across the triangle):

```glsl
in VS_OUT {
    vec3 FragPos;      // Fragment position in world space
    vec3 Normal;       // Surface normal
    vec2 TexCoords;    // Texture coordinates (UV)
} fs_in;
```

### Common Uniforms

#### Textures

```glsl
uniform sampler2D Texture;           // Diffuse/base color texture
uniform sampler2D AOTexture;         // Ambient occlusion map
uniform sampler2D MRTexture;         // Metallic-Roughness map
uniform sampler2D NOTexture;         // Normal map
uniform sampler2D EMTexture;         // Emissive map
uniform sampler2D litsphereTexture;  // Litsphere/MatCap texture
uniform samplerCube skybox;          // Environment cubemap
```

#### Material Properties

```glsl
uniform float AOMult;       // Ambient occlusion multiplier
uniform float RoMult;       // Roughness multiplier
uniform float MeMult;       // Metallic multiplier
uniform float EmMult;       // Emissive multiplier
uniform float NoMult;       // Normal map multiplier
```

#### Lighting

```glsl
uniform vec4 ambientLight;  // RGB + intensity
uniform vec3 viewPos;       // Camera position
uniform bool blinn;         // Use Blinn-Phong instead of Phong
uniform bool useSky;        // Use skybox for reflections
```

#### Point Light Structure

```glsl
struct PointLight {
    vec3 position;   // Light position (world space)
    vec3 color;      // Light color (RGB)
    float intensity; // Light intensity/range
    int type;        // 0 = point light, 1 = directional light
};

uniform PointLight pointLights[3];  // Up to 3 lights
```

### Output

```glsl
out vec4 FragColor;  // RGBA output color
```

## Shader Examples

### Example 1: Simple Phong Lighting

```glsl
#version 330

out vec4 FragColor;

in VS_OUT {
    vec3 FragPos;
    vec3 Normal;
    vec2 TexCoords;
} fs_in;

uniform sampler2D Texture;
uniform vec3 viewPos;
uniform vec4 ambientLight;
uniform PointLight pointLights[3];

void main()
{
    vec3 color = texture(Texture, fs_in.TexCoords).rgb;
    vec3 normal = normalize(fs_in.Normal);
    vec3 viewDir = normalize(viewPos - fs_in.FragPos);
    
    // Ambient
    vec3 ambient = ambientLight.w * color * vec3(ambientLight.xyz);
    
    // Diffuse
    vec3 diffuse = vec3(0.0);
    for (int i = 0; i < 3; i++) {
        if (pointLights[i].intensity > 0.01) {
            vec3 lightDir = normalize(pointLights[i].position - fs_in.FragPos);
            float diff = max(dot(normal, lightDir), 0.0);
            diffuse += diff * pointLights[i].color * color;
        }
    }
    
    FragColor = vec4(ambient + diffuse, 1.0);
}
```

### Example 2: Texture Sampling with Transparency

```glsl
void main()
{
    vec4 texColor = texture(Texture, fs_in.TexCoords);
    vec3 color = texColor.rgb;
    float alpha = texColor.a;
    
    // Discard fully transparent fragments
    if (alpha < 0.01) discard;
    
    // Your lighting calculations here
    vec3 finalColor = color; // ... + lighting
    
    FragColor = vec4(finalColor, alpha);
}
```

### Example 3: Normal Mapping

```glsl
vec3 EvalNormal()
{
    // Calculate tangent-space to world-space transformation
    vec3 pos_dx = dFdx(fs_in.FragPos);
    vec3 pos_dy = dFdy(fs_in.FragPos);
    vec3 tex_dx = dFdx(vec3(fs_in.TexCoords, 0.0));
    vec3 tex_dy = dFdy(vec3(fs_in.TexCoords, 0.0));
    
    vec3 t = (tex_dy.t * pos_dx - tex_dx.t * pos_dy) / 
             (tex_dx.s * tex_dy.t - tex_dy.s * tex_dx.t);
    
    vec3 ng = cross(pos_dx, pos_dy);
    t = normalize(t - ng * dot(ng, t));
    vec3 b = normalize(cross(ng, t));
    mat3 tbn = mat3(t, b, ng);
    
    // Sample and transform normal map
    vec3 normal = texture(NOTexture, fs_in.TexCoords).rgb;
    normal = normalize(tbn * (2.0 * normal - 1.0));
    
    return normal;
}
```

### Example 4: Edge-Based Transparency (X-Ray Effect)

```glsl
void main()
{
    // Calculate opacity based on viewing angle
    float opac = dot(normalize(-fs_in.Normal), 
                     normalize(-fs_in.FragPos));
    
    // Apply falloff and intensity
    opac = ambient + intensity * (1.0 - pow(abs(opac), edgefalloff));
    
    FragColor = vec4(diffuse * opac, opac);
}
```

## Integration with MakeHuman 2

### Shader Loading

Shaders are loaded by the `ShaderRepository` class:

```python
from opengl.shaders import ShaderRepository

# Initialize shader repository
shaders = ShaderRepository(glob)

# Load shaders
shaders.loadShaders(["phong", "pbr", "toon", "litsphere", "xray", "skybox"])

# Get a shader by name
shader = shaders.getShader("pbr")
```

### Shader Selection

Users can select shaders through the Material Editor:

1. **Phong** - Classic lighting (default)
2. **Litsphere** - Image-based lighting
3. **PBR** - Physically based rendering
4. **Toon** - Cartoon/cel shading

### Setting Shader Uniforms

The application sets uniform values before rendering:

```python
# Set uniform values
shaders.setShaderUniform(shader, "viewPos", camera_position)
shaders.setShaderUniform(shader, "ambientLight", ambient_color)

# Set array/struct members
shaders.setShaderArrayStruct(shader, "pointLights", 0, "position", light_pos)
shaders.setShaderArrayStruct(shader, "pointLights", 0, "color", light_color)
shaders.setShaderArrayStruct(shader, "pointLights", 0, "intensity", light_intensity)
```

### Material-Specific Shader Usage

Different materials can use different shaders:

**In .mhmat files:**
```
# Material file
name MyMaterial
diffuseColor 1.0 1.0 1.0
shader phong
```

**Shader types available:**
- `phong` - Default Phong shading
- `litsphere` - Requires litsphere texture
- `pbr` - Physical based rendering
- `toon` - Toon/cel shading

## Shader Configuration File

The `shader.json` file configures global shader settings:

**Location:** `data/shaders/shader.json`

```json
{
    "glclearcolor": [0.2, 0.2, 0.2, 1.0],
    "ambientcolor": [1.0, 1.0, 1.0, 0.35],
    "specularfocus": 30.0,
    "blinn": true,
    "skybox": true,
    "skyboxname": "default",
    "lamps": [
        {
            "position": [8.5, 8.0, 6.5],
            "color": [1.0, 1.0, 1.0, 7.0],
            "type": 0
        }
    ]
}
```

### Configuration Fields

- **`glclearcolor`**: Background color (RGBA)
- **`ambientcolor`**: Ambient light color + intensity
- **`specularfocus`**: Specular highlight sharpness
- **`blinn`**: Use Blinn-Phong (true) or standard Phong (false)
- **`skybox`**: Enable/disable skybox
- **`skyboxname`**: Name of skybox directory
- **`lamps`**: Array of up to 3 light sources
  - **`position`**: Light position [x, y, z]
  - **`color`**: Light color + intensity [r, g, b, intensity]
  - **`type`**: 0 = point light, 1 = directional light

## Lighting System

### Point Light Types

**Type 0 - Point Light:**
- Positioned at specific coordinates
- Light attenuates with distance
- Distance-based intensity calculation

**Type 1 - Directional Light:**
- Position vector treated as direction
- No distance attenuation
- Simulates distant light source (like sun)

### Light Calculation Pattern

```glsl
for (int i = 0; i < 3; i++) {
    if (pointLights[i].intensity > 0.01) {
        vec3 lightDir;
        float attenuation;
        
        if (pointLights[i].type == 0) {
            // Point light
            lightDir = normalize(pointLights[i].position - fs_in.FragPos);
            float distance = length(pointLights[i].position - fs_in.FragPos);
            attenuation = pointLights[i].intensity / (distance * distance);
        } else {
            // Directional light
            lightDir = normalize(pointLights[i].position);
            attenuation = pointLights[i].intensity;
        }
        
        // Calculate lighting contribution
        // ...
    }
}
```

## Common Shader Techniques

### 1. Texture Transparency Handling

```glsl
float transp = texture(Texture, fs_in.TexCoords).a;
if (transp < 0.01) discard;  // Discard fully transparent fragments
```

### 2. Ambient Occlusion Application

```glsl
float ao = texture(AOTexture, fs_in.TexCoords).r;
vec3 finalColor = lighting * ao * AOMult;
```

### 3. Blinn-Phong vs Standard Phong

```glsl
float spec;
if (blinn) {
    vec3 halfwayDir = normalize(lightDir + viewDir);
    spec = pow(max(dot(normal, halfwayDir), 0.0), shininess);
} else {
    vec3 reflectDir = reflect(-lightDir, normal);
    spec = pow(max(dot(viewDir, reflectDir), 0.0), shininess);
}
```

### 4. Fresnel Effect (PBR)

```glsl
vec3 fresnelSchlick(float cosTheta, vec3 F0)
{
    return F0 + (1.0 - F0) * pow(clamp(1.0 - cosTheta, 0.0, 1.0), 5.0);
}
```

### 5. Toon/Cel Shading Steps

```glsl
float diff = max(dot(normal, lightDir), 0.0);
diff = smoothstep(threshold - 0.01, threshold, diff);
```

### 6. Litsphere/MatCap Lookup

```glsl
vec3 normal = fs_in.Normal;
vec2 matcapUV = vec2(normal * vec3(0.495) + vec3(0.5));
vec3 shading = texture2D(litsphereTexture, matcapUV).rgb;
```

## Creating Custom Fragment Shaders

### Step-by-Step Guide

**1. Create the shader file**

Create both vertex and fragment shader files with matching names:
- `myshader.vert`
- `myshader.frag`

**2. Define inputs and outputs**

```glsl
#version 330

out vec4 FragColor;

in VS_OUT {
    vec3 FragPos;
    vec3 Normal;
    vec2 TexCoords;
} fs_in;
```

**3. Declare uniforms**

```glsl
uniform sampler2D Texture;
uniform vec3 viewPos;
uniform vec4 ambientLight;
```

**4. Implement main function**

```glsl
void main()
{
    // Sample textures
    vec3 color = texture(Texture, fs_in.TexCoords).rgb;
    
    // Lighting calculations
    vec3 normal = normalize(fs_in.Normal);
    // ... your custom lighting code
    
    // Output final color
    FragColor = vec4(finalColor, alpha);
}
```

**5. Load and use the shader**

The shader system will automatically load shaders with matching names. To use a custom shader, you can modify `modelling.py` or material files.

### Vertex Shader Compatibility

Your fragment shader must match the vertex shader output:

**Standard vertex shader output:**
```glsl
out VS_OUT {
    vec3 FragPos;
    vec3 Normal;
    vec2 TexCoords;
} vs_out;
```

### Validation Tools

- OpenGL logs show compilation errors
- Use `glGetShaderInfoLog()` for detailed errors
- Test with simpler shaders first
- Verify uniforms are set correctly

## Platform Considerations

### macOS Compatibility

**OpenGL 1.2 Shaders (v12_ prefix):**
- Limited to GLSL version 120
- No geometry shaders
- Reduced feature set
- Automatic fallback on macOS

**Differences:**
```glsl
// Version 330
#version 330
in VS_OUT { ... } fs_in;
out vec4 FragColor;
texture(sampler, coords)

// Version 120
#version 120
varying vec3 FragPos;
gl_FragColor = vec4(...);
texture2D(sampler, coords)
```

### Cross-Platform Tips

1. **Test on target platforms**
2. **Use compatible GLSL features**
3. **Provide version 120 fallbacks**
4. **Avoid platform-specific extensions**
5. **Use standard texture functions**

## Integration with Materials

### Material System Connection

Fragment shaders work with the material system (`.mhmat` files):

**Material properties map to shader uniforms:**
```
diffuseColor → base color
metallicFactor → metallic value
roughnessFactor → roughness value
emissiveFactor → emission intensity
```

### Shader-Specific Material Properties

**PBR shader:**
- Requires metallic-roughness texture
- Supports normal maps
- Uses ambient occlusion
- Emissive mapping

**Litsphere shader:**
- Requires litsphere texture parameter
- `sp_litsphereTexture` property
- `sp_AdditiveShading` blend mode

**Phong shader:**
- Basic diffuse texture
- Optional ambient occlusion
- Specular parameters from light settings

## Summary

Fragment shaders in MakeHuman 2 provide flexible, powerful rendering capabilities:

- **Multiple shader types** for different visual styles
- **Standard GLSL** with version 330 (or 120 for macOS)
- **Material integration** through uniform parameters
- **Lighting system** supporting multiple light types
- **Texture support** including diffuse, normal, metallic, roughness, etc.
- **Extensible** - create custom shaders for unique effects

Understanding fragment shaders enables you to:
- Customize character appearance
- Create new visual styles
- Optimize rendering performance
- Implement advanced rendering techniques
- Integrate with the material system

The shader system is a core part of MakeHuman 2's rendering pipeline, providing the visual quality and flexibility needed for professional character creation.

## Example .frag files

[phong.frag](phong.frag)
[toon.frag](toon.frag)
[xray.frag](xray.frag)
[pbr.frag](pbr.frag)
[litsphere.frag](litsphere.frag)



