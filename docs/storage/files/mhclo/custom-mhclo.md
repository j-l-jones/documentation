
## Best Practices for Creating .mhclo Files

### 1. Vertex Mapping
- **Start with exact fitting** for areas that should closely follow the body (waistband, neckline, cuffs)
- **Use offsets** for loose-fitting areas (fabric draping, wrinkles)
- **Test on different body types** to ensure the clothing deforms properly
- **Use interpolated fitting** for smooth transitions between fitted and loose areas

### 2. Z-depth Management
- **Plan your layering system** before assigning z_depth values
- **Leave gaps** between layers (e.g., underwear at 15, shirt at 35, jacket at 65)
- **Document your z_depth convention** if creating multiple clothing items
- **Test combinations** to ensure no Z-fighting occurs

### 3. Vertex Deletion
- **Test layering** to ensure under-layers aren't over-deleted
- **Use continuous ranges** (`start - end`) for better performance
- **Document which body parts** are deleted for future reference

### 4. Scaling and Transformation
- **Choose appropriate reference vertices** that represent the dimension you're scaling
- **Test with extreme body shapes** (very tall, very short, very thin, very heavy)
- **Use multiple scale parameters** (x, y, z) for best results
- **Avoid scaling if possible** - pre-model for the average body size

### 5. Materials
- **Create separate material files** for each color/texture variant
- **Use descriptive material names** (e.g., `jeans_blue.mhmat`, `jeans_black.mhmat`)
- **Include normal maps** for fabric detail
- **Test materials** under different lighting conditions

### 6. Tagging
- **Use standard tags** for better discoverability
- **Include gender tags** even for unisex items
- **Add style and category tags** for filtering
- **Use descriptive tags** that users might search for

## Tools and Workflow

### Creating Clothing from Scratch

1. **Model the clothing mesh** in Blender or another 3D application
2. **Use the MakeHuman base mesh** as a reference for fitting
3. **UV unwrap** the clothing mesh
4. **Export as OBJ** with proper naming
5. **Generate vertex mapping** using:
   - MakeHuman Blender addon (MPFB2) - automatic mapping tools
   - Manual calculation - for precise control
   - Python scripts - for procedural generation
6. **Create the .mhclo file** with metadata and mapping data
7. **Create the .mhmat material file**
8. **Test in MakeHuman** with various body types and poses
9. **Iterate** on fit, mapping, and deletion masks

### Converting Existing Clothing

If you have clothing OBJ files without mapping data:

1. **Import both** the base human and clothing mesh into Blender
2. **Use MPFB2 addon** to generate vertex mapping
3. **Export the mapping** as a .mhclo file
4. **Refine the mapping** manually if needed
5. **Test and iterate**

### Updating from MakeHuman v1

If you have clothing from MakeHuman v1:

- The `.mhclo` format is largely compatible
- Update `basemesh` parameter to match your base (e.g., `hm08`)
- Check vertex indices if the base mesh has changed
- Update z_depth values to match the new layering system
- Test thoroughly as some parameters may be deprecated


## Debugging Common Issues

### Clothing doesn't fit properly
- Check that vertex mapping covers all vertices in the OBJ
- Verify reference vertex indices are valid
- Test scale parameters with different body types
- Check for proper weight distribution (weights should sum to ~1.0)

### Body shows through clothing
- Expand the `delete_verts` range to cover exposed areas
- Add small offsets to move clothing away from body
- Check z_depth to ensure proper rendering order

### Clothing doesn't deform with body
- Verify vertex mapping is correct
- Check that scale parameters reference appropriate vertices
- Ensure transformation matrix is calculated properly

### Z-fighting (flickering surfaces)
- Adjust z_depth values to separate layers
- Ensure no two clothing items have the same z_depth
- Check that offsets don't place vertices at identical positions

## Performance Considerations

- **Vertex count**: Keep clothing meshes optimized (typically 2,000-10,000 vertices)
- **Deletion mask size**: Minimize the number of deleted vertices
- **Mapping complexity**: Use exact fitting where possible (faster than interpolation)
- **Binary format**: Always use compiled `.mhpxy` for production
- **Material complexity**: Optimize textures and shader complexity

## Related Documentation

- [.proxy file](proxy.md) - Generic proxy file format (same format as .mhclo)
- [.mhmat file](mhmat.md) - Material file format
- [.mhw file](mhw.md) - Vertex bone weights file format
- [custom_proxy.md](custom_proxy.md) - Guide to creating custom proxies

## References

- **Source Code**: `core/attached_asset.py` - Asset loading and management
- **Source Code**: `obj3d/object3d.py` - 3D object handling
- **Compilation**: The system automatically compiles ASCII to binary format

## License Note

When creating clothing assets:
- Specify the license in the `.mhclo` file
- Include license information in accompanying files
- CC0 (public domain) is recommended for maximum compatibility
- CC-BY 4.0 allows commercial use with attribution

## Example Files

For complete working examples, see the clothing files in:
- `data/clothes/hm08/` - System clothing examples
- Look for files with `.mhclo` extension

---

**Note**: The `.mhclo` format is identical to the `.proxy` format at the parser level. The distinction is primarily organizational and semantic - `.mhclo` files are specifically for clothing and are stored in the `clothes/` directory with specific tagging conventions.
