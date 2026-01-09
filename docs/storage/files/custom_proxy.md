

<!--
cool stuff for me to try later
maybe put in a different doc 
-->
## Creating Custom Proxies

### Step 1: Create the OBJ Mesh

Model your proxy mesh in a 3D modeling application (Blender, Maya, etc.). The mesh should be:
- Positioned and scaled to fit the base MakeHuman human
- Properly UV-unwrapped
- Exported as OBJ format

### Step 2: Generate Vertex Mapping

You have several options:

#### Option A: Exact Fitting (Simple)
If your proxy vertices align exactly with human vertices:
```python
verts
<human_vertex_index_1>
<human_vertex_index_2>
...
```

#### Option B: Blender MakeHuman Plugin
Use the MakeHuman Blender plugin (MPFB2) to:
1. Import the base human mesh
2. Model your proxy
3. Use the proxy fitting tools to generate vertex mappings
4. Export as .proxy or .mhclo file

#### Option C: Manual Calculation
For each proxy vertex, find the nearest triangle on the human mesh and calculate barycentric coordinates.

### Step 3: Write the Proxy File

Create a `.proxy` or `.mhclo` text file with:
- Metadata (name, UUID, description, tags)
- References to OBJ and material files
- Render properties (z_depth, max_pole)
- Vertex mapping data
- Deletion mask (if needed)

### Step 4: Create Material File

Create a `.mhmat` file defining textures and shader properties. See the MakeHuman material documentation for details.

### Step 5: Test and Iterate

1. Place files in `~/makehuman/v1py3/data/<proxytype>/`
2. Launch MakeHuman
3. Navigate to the appropriate library tab
4. Select your proxy
5. Test with different body shapes, poses, and expressions
6. Adjust vertex mapping and deletion mask as needed

## Python API

### Loading a Proxy

```python
import proxy
from human import Human

human = Human()
pxy = proxy.loadProxy(human, "path/to/proxy.mhclo", type="Clothes")
```

### Accessing Proxy Data

```python
# Get proxy vertex positions
coords = pxy.getCoords(fit_to_posed=False)

# Get vertex mapping
ref_indices = pxy.ref_vIdxs  # Shape: (n_verts, 3)
weights = pxy.weights         # Shape: (n_verts, 3)
offsets = pxy.offsets         # Shape: (n_verts, 3)

# Get reverse mapping (which proxy verts depend on human vert)
proxy_verts = pxy.vertWeights[human_vert_idx]  # List of (proxy_vert, weight)

# Check if proxy has custom bone weights
has_custom = pxy.hasCustomVertexWeights()

# Get vertex bone weights for rigging
vertex_bone_weights = pxy.getVertexWeights(human_weights, skeleton)
```

### Creating a Proxy Programmatically

```python
from proxy import Proxy

pxy = Proxy(filepath, proxyType, human)
pxy.name = "My Custom Proxy"
pxy.uuid = "unique-id-here"
pxy.z_depth = 50
pxy.max_pole = 8
pxy._obj_file = "mesh.obj"
pxy._material_file = "material.mhmat"

# Set up vertex mapping data
pxy.ref_vIdxs = np.array([[v1, v2, v3], ...])
pxy.weights = np.array([[w1, w2, w3], ...])
pxy.offsets = np.array([[dx, dy, dz], ...])

# Finalize
pxy._reloadReverseMapping()

# Save as binary
from proxy import saveBinaryProxy
saveBinaryProxy(pxy, "output.mhpxy")
```

## Common Pitfalls

1. **Vertex count mismatch**: The `verts` section must have exactly as many lines as the OBJ has vertices
2. **Weights don't sum to 1.0**: Ensure interpolation weights sum to approximately 1.0
3. **Wrong z_depth**: Proxies rendering in wrong order (e.g., hair behind head)
4. **Missing deletion mask**: Base mesh visible through proxy (e.g., scalp through hair)
5. **Outdated compiled files**: Delete `.mhpxy` files to force recompilation if you edit `.proxy`/`.mhclo` files
6. **Path references**: All file paths in proxy files are relative to the proxy file's directory
