

<!--
cool stuff for me to try later
maybe put in a different doc 
-->
## Creating Custom Materials: Step-by-Step Guide

### Workflow Overview

Creating a custom material involves:
1. Planning your material (type, textures, properties)
2. Preparing texture assets
3. Writing the `.mhmat` file
4. Testing and iterating
5. Deploying for use

### Example: Creating a Custom Skin Material

Let's walk through creating a custom skin material with unique textures and properties.

#### Step 1: Prepare Your Textures

For a realistic skin material, you'll typically need:

**Required**:
- **Diffuse/Color Map** (`skin_diffuse.png`) - Base skin color texture
  - Resolution: 2048×2048 or 4096×4096
  - Format: PNG (with alpha) or JPG
  - Color space: sRGB
  - Should include skin tone, freckles, blemishes, makeup

**Optional but Recommended**:
- **Normal Map** (`skin_normal.png`) - Surface detail (pores, wrinkles)
  - RGB normal map format (tangent space)
  - Blue channel pointing outward
- **Specular Map** (`skin_specular.png`) - Controls shininess variation
  - Grayscale: white = shiny (oily areas), black = matte (dry areas)
- **Ambient Occlusion Map** (`skin_ao.png`) - Shadow detail in crevices
  - Grayscale: black = occluded areas, white = exposed areas

**Texture Tips**:
- Use seamless textures or ensure proper UV mapping
- Keep texture sizes power-of-2 (512, 1024, 2048, 4096)
- Test on actual 3D model to check for seams or distortion
- Consider mipmaps for better performance and quality

#### Step 2: Create Directory Structure

Organize your files properly:

```
~/makehuman/v1py3/data/skins/myskin/
├── myskin.mhmat              # Material definition file
├── textures/
│   ├── skin_diffuse.png      # Color/albedo map
│   ├── skin_normal.png       # Normal map
│   ├── skin_specular.png     # Specular/glossiness map
│   └── skin_ao.png           # Ambient occlusion map
└── preview.png               # Optional: thumbnail for selection UI
```

#### Step 3: Write the Material File

Create `myskin.mhmat` with appropriate properties:

```mhmat
# Custom skin material - Fantasy Elf Skin
# Author: Your Name
# License: CC-BY 4.0
# Created: 2026-01-09

name Fantasy_Elf_Skin
description Custom fantasy elf skin with pale complexion and subtle shimmer
tag fantasy
tag elf
tag custom
tag skin

# Color properties
# Slightly cooler tones for fantasy feel
ambientColor 0.12 0.12 0.13
diffuseColor 1.0 1.0 1.0
specularColor 0.4 0.4 0.45
shininess 0.6
emissiveColor 0.0 0.0 0.0
opacity 1.0
translucency 0.15

# Rendering flags
shadeless false
wireframe false
transparent false
alphaToCoverage true
backfaceCull true
depthless false
castShadows true
receiveShadows true

# Don't use auto skin blending - we want our custom colors
autoBlendSkin false

# Sub-surface scattering for realistic skin
sssEnabled true
sssRScale 5.0
sssGScale 2.5
sssBScale 1.5

# Texture maps (relative to this .mhmat file)
diffuseTexture textures/skin_diffuse.png
normalmapTexture textures/skin_normal.png
normalmapIntensity 0.7
specularmapTexture textures/skin_specular.png
specularmapIntensity 0.8
aomapTexture textures/skin_ao.png
aomapIntensity 0.5

# Shader configuration
shader data/shaders/glsl/litsphere

# Enable features we're using
shaderConfig diffuse true
shaderConfig normal true
shaderConfig spec true
shaderConfig ambientOcclusion true
shaderConfig bump false
shaderConfig displacement false
shaderConfig vertexColors false
shaderConfig transparency false
```

#### Step 4: Test the Material

**In MakeHuman Application**:

1. **Launch MakeHuman** and load a character
2. Navigate to **Files → Settings → Paths** and verify user data path
3. Go to **Geometries → Topologies** tab
4. Select **Skin** library
5. Look for your material in the list (may need to scroll or refresh)
6. Click to apply your material
7. Rotate view and test under different angles

**Command Line Testing**:

```python
# Test script: test_material.py
import sys
sys.path.insert(0, 'makehuman')

import material
import os

# Load your material
mat = material.fromFile(os.path.expanduser(
    "~/makehuman/v1py3/data/skins/myskin/myskin.mhmat"
))

# Verify properties
print(f"Material loaded: {mat.name}")
print(f"Diffuse texture: {mat.diffuseTexture}")
print(f"Normal map: {mat.normalMapTexture}")
print(f"SSS enabled: {mat.sssEnabled}")
print(f"Shader: {mat.shader}")

# Check for issues
if mat.diffuseTexture and not os.path.exists(mat.diffuseTexture):
    print(f"WARNING: Diffuse texture not found: {mat.diffuseTexture}")
if mat.normalMapTexture and not os.path.exists(mat.normalMapTexture):
    print(f"WARNING: Normal map not found: {mat.normalMapTexture}")
```

#### Step 5: Iterate and Refine

Common adjustments:

**Too Shiny**:
```mhmat
shininess 0.3              # Reduce from 0.6
specularmapIntensity 0.5   # Reduce intensity
```

**Too Dark**:
```mhmat
ambientColor 0.15 0.15 0.15    # Increase ambient
# Or adjust diffuse texture brightness in image editor
```

**Not Enough Detail**:
```mhmat
normalmapIntensity 1.0     # Increase from 0.7
aomapIntensity 0.8         # Increase from 0.5
```

**SSS Too Strong**:
```mhmat
sssRScale 3.0              # Reduce from 5.0
sssGScale 1.5              # Reduce from 2.5
sssBScale 1.0              # Reduce from 1.5
```

**Color Appears Wrong**:
```mhmat
# Add color tint via diffuse color
diffuseColor 1.0 0.95 0.9  # Warmer tone
# Or
diffuseColor 0.95 0.95 1.0 # Cooler tone
```

### Example: Creating a Custom Clothing Material

For clothing with fabric texture:

```mhmat
# Custom denim material
name Denim_Blue_Worn
description Worn blue denim fabric with realistic texture
tag clothing
tag fabric
tag denim

ambientColor 0.05 0.06 0.08
diffuseColor 0.25 0.35 0.5
specularColor 0.15 0.15 0.15
shininess 0.25
opacity 1.0
translucency 0.0

shadeless false
transparent false
backfaceCull true
castShadows true
receiveShadows true

# Denim textures
diffuseTexture textures/denim_diffuse.png
normalmapTexture textures/denim_normal.png
normalmapIntensity 0.9
specularmapTexture textures/denim_spec.png
specularmapIntensity 0.6
aomapTexture textures/denim_ao.png
aomapIntensity 0.7

shader data/shaders/glsl/phong

shaderConfig diffuse true
shaderConfig normal true
shaderConfig spec true
shaderConfig ambientOcclusion true
```

### Example: Creating a Stylized/Toon Material

For non-photorealistic rendering:

```mhmat
# Toon/Cel-shaded material
name Toon_Skin_Warm
description Stylized cartoon skin with flat shading
tag toon
tag cartoon
tag stylized

# Flat colors for toon look
ambientColor 0.3 0.3 0.3
diffuseColor 1.0 0.85 0.75
specularColor 1.0 1.0 1.0
shininess 0.95
opacity 1.0

shadeless false
backfaceCull true
castShadows true
receiveShadows true

# Optional: simple diffuse for color zones
diffuseTexture textures/toon_skin_zones.png

# Use toon shader for cel-shading effect
shader data/shaders/glsl/toon

# Simple config - no normal/spec maps for flat look
shaderConfig diffuse true
shaderConfig normal false
shaderConfig spec false
shaderConfig bump false
```

### Advanced: Material Variants

Create multiple variants by copying and modifying:

```bash
# Base material
~/makehuman/v1py3/data/skins/myskin/myskin_base.mhmat

# Variants
~/makehuman/v1py3/data/skins/myskin/myskin_pale.mhmat
~/makehuman/v1py3/data/skins/myskin/myskin_tan.mhmat
~/makehuman/v1py3/data/skins/myskin/myskin_dark.mhmat
```

Each variant shares textures but adjusts colors:

```mhmat
# myskin_pale.mhmat
name MySkin_Pale
diffuseColor 1.0 0.98 0.95
specularColor 0.3 0.3 0.32
translucency 0.2
# ... rest of properties
```

### Troubleshooting Custom Materials

#### Material Not Appearing in UI

**Causes**:
- File not in correct directory
- Filename doesn't match expected pattern
- Permission issues

**Solutions**:
```bash
# Check file location
ls -la ~/makehuman/v1py3/data/skins/

# Check file permissions
chmod 644 ~/makehuman/v1py3/data/skins/myskin/myskin.mhmat

# Restart MakeHuman to refresh material list
```

#### Textures Show as Black/Missing

**Causes**:
- Wrong texture path (absolute instead of relative)
- Texture file doesn't exist
- Unsupported format

**Solutions**:
```mhmat
# Use relative paths from material file location
diffuseTexture textures/skin_diffuse.png  # Good
# NOT: /home/user/textures/skin_diffuse.png  # Bad - absolute path

# Verify texture exists
# ls ~/makehuman/v1py3/data/skins/myskin/textures/

# Convert to supported format if needed
# convert skin.tga skin.png
```

#### Material Looks Wrong/Broken

**Quick diagnostics**:

```python
# diagnose_material.py
import material

mat = material.fromFile("path/to/material.mhmat")

print("=== Material Diagnostics ===")
print(f"Name: {mat.name}")
print(f"Shader: {mat.shader}")
print(f"Diffuse color: {mat.diffuseColor}")
print(f"Shininess: {mat.shininess}")
print(f"Transparent: {mat.transparent}")
print(f"Opacity: {mat.opacity}")
print()
print("Textures:")
print(f"  Diffuse: {mat.diffuseTexture}")
print(f"  Normal: {mat.normalMapTexture}")
print(f"  Specular: {mat.specularMapTexture}")
print()
print("Shader config:")
for key, val in mat.shaderConfig.items():
    print(f"  {key}: {val}")
```

### Best Practices for Custom Materials

1. **Start Simple**: Begin with basic colors and no textures, add complexity gradually
2. **Test Incrementally**: Add one texture at a time and verify it works
3. **Use Relative Paths**: Always use paths relative to the `.mhmat` file
4. **Document Your Work**: Add comments explaining choices and parameters
5. **Version Your Materials**: Keep old versions when making changes
6. **Share Appropriately**: Include license information for redistribution
7. **Optimize Textures**: Don't use unnecessarily large textures
8. **Test on Multiple Characters**: Verify material works with different body types/proportions

### Material Templates

#### Template: Basic Skin

```mhmat
name YourSkinName
description Your description here
tag skin
tag custom

ambientColor 0.11 0.11 0.11
diffuseColor 1.0 1.0 1.0
specularColor 0.3 0.3 0.3
shininess 0.5
opacity 1.0
translucency 0.1

autoBlendSkin false
sssEnabled true
sssRScale 4.0
sssGScale 2.0
sssBScale 1.0

diffuseTexture textures/diffuse.png
normalmapTexture textures/normal.png
normalmapIntensity 0.8
specularmapTexture textures/specular.png
specularmapIntensity 0.7

shader data/shaders/glsl/litsphere

shaderConfig diffuse true
shaderConfig normal true
shaderConfig spec true
```

#### Template: Basic Clothing

```mhmat
name YourClothingName
description Your description here
tag clothing
tag custom

ambientColor 0.1 0.1 0.1
diffuseColor 0.8 0.8 0.8
specularColor 0.2 0.2 0.2
shininess 0.3
opacity 1.0

backfaceCull true
castShadows true
receiveShadows true

diffuseTexture textures/diffuse.png
bumpmapTexture textures/bump.png
bumpmapIntensity 0.6

shader data/shaders/glsl/phong

shaderConfig diffuse true
shaderConfig bump true
```

## Python API

### Loading a Material

```python
import material

# Load from file
mat = material.fromFile("path/to/material.mhmat")

# Access properties
print(f"Material: {mat.name}")
print(f"Diffuse: {mat.diffuseColor}")
print(f"Shininess: {mat.shininess}")
print(f"Transparent: {mat.transparent}")
```

### Creating a Material

```python
from material import Material, Color

# Create new material
mat = Material("MyMaterial")

# Set properties
mat.description = "Custom material"
mat.addTag("custom")

# Set colors
mat.ambientColor = Color(0.1, 0.1, 0.1)
mat.diffuseColor = Color(0.8, 0.6, 0.4)
mat.specularColor = Color(0.3, 0.3, 0.3)
mat.shininess = 0.7
mat.opacity = 1.0

# Set textures
mat.diffuseTexture = "textures/diffuse.png"
mat.bumpMapTexture = "textures/bump.png"
mat.bumpMapIntensity = 0.8

# Set rendering flags
mat.transparent = False
mat.backfaceCull = True
mat.castShadows = True

# Set shader
mat.shader = "data/shaders/glsl/phong"
mat.setShaderParameter("customParam", 0.5)

# Configure shader features
mat.configureShading(
    diffuse=True,
    bump=True,
    normal=False,
    spec=True
)

# Save to file
mat.toFile("materials/mymaterial.mhmat")
```

### Modifying Material Properties

```python
# Clone existing material
new_mat = mat.clone()
new_mat.name = "ModifiedMaterial"

# Modify colors
new_mat.diffuseColor.r = 1.0
new_mat.diffuseColor.g = 0.5
new_mat.diffuseColor.b = 0.3

# Color arithmetic
darker = mat.diffuseColor * 0.5
lighter = mat.diffuseColor + Color(0.1, 0.1, 0.1)

# Access shader uniforms
if mat.shaderObj:
    uniforms = mat.shaderUniforms
    print(f"Available uniforms: {uniforms}")

# Get shader parameters
params = mat.shaderParameters
print(f"Diffuse: {params['diffuse']}")
print(f"Ambient: {params['ambient']}")
```

### Applying Material to Object

```python
# Apply to mesh object
obj.material = mat

# Copy material from another object
obj.material = obj2.material.clone()

# Access object's material
current_mat = obj.material
print(f"Object uses material: {current_mat.name}")
```

## Best Practices

### Material Design

1. **Use appropriate colors**: Keep color values realistic (most materials don't exceed 0.8-0.9)
2. **Shininess balance**: Most materials use 0.2-0.7; only very shiny materials need > 0.9
3. **Texture optimization**: Use power-of-2 texture sizes (512, 1024, 2048) for GPU efficiency
4. **Transparency carefully**: Enable `transparent` only when needed (performance impact)
5. **Test with different lighting**: Verify material appearance under various lighting conditions

### Performance

1. **Minimize texture count**: Use only necessary texture maps
2. **Appropriate texture resolution**: Don't use 4K textures for small objects
3. **Shader complexity**: Complex shaders can impact performance on older hardware
4. **Alpha-to-coverage**: Use for better quality transparent edges with minimal performance cost
5. **Disable unused features**: Set `shaderConfig` options to `false` for unused features

### Portability

1. **Relative paths**: Use relative paths for textures (from material file location)
2. **Standard formats**: Use PNG or JPG for maximum compatibility
3. **Shader availability**: Test with different shader options for fallback
4. **Document dependencies**: Note required textures and shaders in comments

### Organization

1. **Consistent naming**: Use descriptive names (e.g., `Skin_Caucasian_Female`)
2. **Tag appropriately**: Add relevant tags for searchability
3. **Group materials**: Store related materials in same folder
4. **Version control**: Keep `.mhmat` files in version control

## Common Use Cases

### Skin Materials

Key properties:
- `autoBlendSkin true` for adaptive skin tone
- SSS enabled with appropriate scales
- Litsphere shader for realistic appearance
- Moderate shininess (0.3-0.5)

### Eye Materials

Key properties:
- High shininess (0.9-1.0)
- Transparent true with alpha-to-coverage
- Diffuse texture for iris detail
- Litsphere shader with custom litsphere texture

### Hair Materials

Key properties:
- Anisotropic specular highlighting (shader-dependent)
- Transparency for alpha-masked hair
- Moderate to high shininess (0.5-0.8)
- Specular map for varied glossiness

### Clothing Materials

Key properties:
- Bump/normal maps for fabric texture
- Specular maps for varied surface properties
- Optional transparency for lace, mesh fabrics
- Backface culling usually enabled (unless double-sided fabric)

### Metallic Materials

Key properties:
- High shininess (0.8-1.0)
- Strong specular color matching diffuse
- Optional emission for polished appearance
- Specular maps for surface variation

## Troubleshooting

### Material Not Loading

**Problem**: Material file not found or not loading

**Solutions**:
- Check file path is correct
- Verify file encoding is UTF-8
- Check for syntax errors (use a linter)
- Ensure all referenced texture files exist

### Textures Not Appearing

**Problem**: Texture maps not visible

**Solutions**:
- Verify texture paths are correct (relative to material file)
- Check `shaderConfig` enables the texture type (e.g., `diffuse true`)
- Ensure shader supports the texture type
- Verify texture file format is supported

### Transparency Issues

**Problem**: Transparent materials render incorrectly

**Solutions**:
- Set `transparent true` in material
- Adjust `opacity` value (< 1.0)
- Use `alphaToCoverage true` for better edges
- Consider transparency map for per-pixel control
- Check render order (transparent objects render last)

### Shader Errors

**Problem**: Shader fails to compile or render incorrectly

**Solutions**:
- Verify shader file exists
- Check shader compatibility with material properties
- Review shader console output for errors
- Try fallback shader (e.g., `glsl/phong`)
- Ensure `shaderConfig` options match shader capabilities
