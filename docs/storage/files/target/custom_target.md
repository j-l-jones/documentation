

<!--
cool stuff for me to try later
maybe put in a different doc 
-->
## Creating Custom Targets

## Creating Target Files

### Method 1: Save from Modified Mesh

```python
import algos3d

# After modifying mesh vertices
algos3d.saveTranslationTarget(
    obj=human.meshData,
    targetPath="path/to/new_target.target",
    groupToSave=None,      # Optional: specific face group
    epsilon=0.001          # Minimum movement threshold (1mm)
)
```

### Method 2: Manual Creation

1. **Identify vertices to modify** (using 3D modeling software or MakeHuman)
2. **Calculate displacement vectors** relative to base mesh
3. **Write ASCII file**:

```
# author: Your Name
# license: CC0
# basemesh hm08

1234 0.001 0.0 0.0
1235 0.0015 0.0005 0.0
5678 -0.001 0.0 0.001
```

4. **Test in MakeHuman**: Place in `data/targets/` and load
5. **Compile to NPZ**: Run `compile_targets.py` for production use

### Method 3: MakeTarget Tool

MakeHuman includes a modeling tool for creating targets:

1. Load base mesh in Blender with MakeTarget plugin
2. Sculpt modifications
3. Export as `.target` file
4. Target plugin automatically calculates vertex displacements

## Loading and Applying Targets

### Loading a Target

```python
import algos3d

# Load target (cached automatically)
target = algos3d.getTarget(human.meshData, "data/targets/nose/nose-width-incr.target")

# Access target data
print(f"Target affects {len(target.verts)} vertices")
print(f"First vertex: {target.verts[0]} moves by {target.data[0]}")
```

### Applying a Target

```python
# Apply with 50% strength
algos3d.loadTranslationTarget(
    obj=human.meshData,
    targetPath="data/targets/nose/nose-width-incr.target",
    morphFactor=0.5,           # 0.0-1.0 (or beyond)
    faceGroupToUpdateName=None, # Optional: limit to face group
    update=True,               # Update mesh display
    calcNorm=True,             # Recalculate normals
    scale=[1.0, 1.0, 1.0],    # Optional: scale per axis
    animatedMesh=None          # Optional: pose-aware application
)
```

### Refreshing Cached Targets

```python
# Force reload from disk (useful during development)
algos3d.refreshCachedTarget("data/targets/nose/nose-width-incr.target")
```

## Target Organization and Discovery

### Target Indexing System

MakeHuman builds an index of all targets at startup:

```python
from targets import getTargets

targets = getTargets()

# Get targets by exact group
nose_targets = targets.getTargetsByGroup('nose-width-incr')

# Find targets by partial group name
nose_all = targets.findTargets('nose')  # All nose-related targets

# Debug print all groups
targets.debugGroups()

# Debug print all targets with details
targets.debugTargets(showData=True)
```

### Group Hierarchy

Targets are organized hierarchically:

```
targets/
  nose/
    nose-width-incr.target           → Group: ['nose', 'width', 'incr']
    nose-width-decr.target           → Group: ['nose', 'width', 'decr']
    nose-length-incr.target          → Group: ['nose', 'length', 'incr']
  breast/
    breast-dist-incr-female.target   → Group: ['breast', 'dist', 'incr']
                                        Depends: gender=female
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

### Compilation Details

```python
# Per target, compile_targets.py does:
target._load_text("path/to/target.target")
iname, vname, lname = target._save_binary("path/to/target.target")

# Creates temporary files:
# - path/to/target.index.npy
# - path/to/target.vector.npy
# - path/to/target.license.npy (if custom license)

# Then adds to ZIP archive and removes temp files
```

## Advanced Features

### Pose-Aware Target Application

Targets can be applied in a pose-aware manner, transforming the displacement vectors through the current bone transformations:

```python
algos3d.loadTranslationTarget(
    obj=human.meshData,
    targetPath="data/targets/body/body-muscular-incr.target",
    morphFactor=0.7,
    animatedMesh=human.getAnimatedMesh()  # Apply in current pose
)
```

This ensures targets deform correctly even when the character is posed.

### Face Group-Specific Application

Apply targets only to specific parts of the mesh:

```python
algos3d.loadTranslationTarget(
    obj=human.meshData,
    targetPath="target.target",
    morphFactor=1.0,
    faceGroupToUpdateName="head"  # Only affect head vertices
)
```

### Target Caching

Targets are cached in memory after first load:

```python
# First load: reads from disk
target1 = algos3d.getTarget(obj, "path/to/target.target")

# Second load: returns cached instance
target2 = algos3d.getTarget(obj, "path/to/target.target")

assert target1 is target2  # Same object
```

Cache is stored in `algos3d._targetBuffer` dictionary.


## Workflow Example: Creating a Custom Target

### Step 1: Export Base Mesh

```python
from export import exportObj

exportObj("base.obj", human.meshData)
```

### Step 2: Sculpt in External Tool

1. Import `base.obj` into Blender/Maya/ZBrush
2. Sculpt modifications (e.g., larger nose)
3. Export modified mesh as `modified.obj`

### Step 3: Calculate Difference

```python
import numpy as np
from files3d import loadMesh

base = loadMesh("base.obj")
modified = loadMesh("modified.obj")

# Calculate displacement
delta = modified.coord - base.coord

# Find vertices that moved
epsilon = 0.001  # 1mm threshold
moved = np.where(np.sum(delta**2, axis=1) > epsilon**2)[0]

# Write target file
with open("nose-bigger.target", "w") as f:
    f.write("# author: Your Name\n")
    f.write("# license: CC0\n")
    f.write("# basemesh hm08\n\n")
    for v in moved:
        f.write(f"{v} {delta[v,0]} {delta[v,1]} {delta[v,2]}\n")
```

### Step 4: Test and Iterate

1. Copy `nose-bigger.target` to `data/targets/nose/`
2. Restart MakeHuman
3. Apply target and test with various body shapes
4. Refine as needed

### Step 5: Compile for Production

```bash
python compile_targets.py
```

## Best Practices

### Target Design

1. **Minimize vertex count**: Only include vertices that actually move
2. **Use epsilon threshold**: Filter out tiny movements (< 0.001m)
3. **Test with extremes**: Verify target works at 0.0, 1.0, and beyond
4. **Consider symmetry**: Create symmetric targets for left/right body parts
5. **Logical naming**: Follow MakeHuman's naming conventions
6. **Document dependencies**: Use macro variable keywords appropriately

### Unexpected Deformations

**Problem**: Target causes strange mesh artifacts

**Solutions**:
- Verify vertex indices are valid (< mesh vertex count)
- Check displacement magnitudes are reasonable (< 0.1m typically)
- Test with morphFactor=0.5 to see if effect is too strong
- Ensure base mesh matches target's base mesh version