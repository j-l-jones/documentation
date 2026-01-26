---
title: ".mhm file"
description: "MakeHuman model file"
---

## .mhm file
# MakeHuman2 model file (.mhm)

## Overview

The `.mhm` file format is the primary model file format used by MakeHuman2 to save and load complete character definitions. It is a human-readable text format that stores all the information needed to recreate a character, including:

- Character metadata (name, author, version, tags)
- Body shape modifiers (targets)
- Attached assets (clothes, hair, eyes, etc.)
- Material assignments
- Skeleton/rig information

### Creation
.mhm files are generated via File -> Save and stored in the user data directory under /models/

\<UserDataPath\>/data/models/\<mesh_name\>/

<img src="file-save-mhm.png" alt="MHM file save dialog" width="400">

### Example .mhm file

\# MakeHuman2 Model File
version v2.0.1\
name foo\
author foo\
uuid 4315e863-03d7-44cd-a207-b2d797ee54e7\
tags example;foo\
eyes HighPolyEyes 2c12f43b-1303-432c-b7ce-d78346baf2e6\
clothes female_casualsuit01 a8b0e841-144b-4f03-8b9e-ea8fdce8c863\
clothes fedora 566dbd52-71d1-4d76-a799-0474a5a384db\
hair bob02 cd7840cf-b340-49e9-a505-8a5a22a86db2\
skinMaterial skins/default.mhmat\
material HighPolyEyes 2c12f43b-1303-432c-b7ce-d78346baf2e6 eyes/materials/brown.mhmat


### File Header

Every `.mhm` file starts with a header section containing metadata:

```
# MakeHuman2 Model File
version v2.0.1
name MyCharacter
author John Doe
uuid 550e8400-e29b-41d4-a716-446655440000
tags fantasy;male;warrior
```

## Keyword Reference

### Metadata Keywords

#### `version`
**Syntax:** `version v<major>.<minor>.<patch>`

**Description:** Specifies the version of MakeHuman2 that created the file.

**Example:**
```
version v2.0.1
```

#### `name`
**Syntax:** `name <character name>`

**Description:** The name of the character. Can contain spaces and multiple words.

**Example:**
```
name Fantasy Hero Character
```

#### `author`
**Syntax:** `author <author name>`

**Description:** The name of the character's creator. Can contain spaces.

**Example:**
```
author Foo Bird
```
**Default:** `unknown` if not specified

#### `uuid`
**Syntax:** `uuid <uuid-string>`

**Description:** A unique identifier for the character in standard UUID format.

**Example:**
```
uuid 550e8400-e29b-41d4-a716-446655440000
```
**Optional:** This field is not required.

#### `tags`
**Syntax:** `tags <tag1>;<tag2>;<tag3>`

**Description:** Semicolon-separated list of tags for categorizing and searching characters.

**Example:**
```
tags fantasy;male;warrior;medieval
```
**Optional:** Can be omitted if no tags are needed.

---

### Body Shape Keywords

#### `modifier`
**Syntax:** `modifier <target-path> <value>`

**Description:** Applies a morphing target to modify the base mesh. The target path follows the pattern `category/TargetName`, and the value is typically a float between 0.0 and 1.0 (though some modifiers may accept values outside this range).

**Common Modifier Categories:**
- `macrodetails/` - High-level body characteristics (Gender, Age)
- `macrodetails-universal/` - Universal body modifiers (Muscle, Weight)
- `macrodetails-height/` - Height adjustments
- `macrodetails-proportions/` - Body proportions
- `breast/` - Breast-related modifiers
- `face/` - Facial features
- `body/` - Detailed body parts

**Examples:**
```
modifier macrodetails/Gender 0.5
modifier macrodetails/Age 0.25
modifier macrodetails-universal/Muscle 0.7
modifier macrodetails-universal/Weight 0.45
modifier macrodetails-height/Height 0.6
modifier breast/BreastSize 0.5
modifier breast/BreastFirmness 0.5
```

**Value Range:** Typically 0.0 to 1.0, where 0.5 is often the neutral/default position.

**Note:** Only modifiers that differ from their default value are saved in the file.

---

### Asset Keywords

These keywords define attached 3D assets like clothing, hair, eyes, etc.

#### `clothes`
**Syntax:** `clothes <asset-name> <uuid>`

**Description:** Attaches a clothing item to the character.

**Example:**
```
clothes casual_shirt 7c8f2a1b-4d5e-6789-0123-456789abcdef
```

#### `hair`
**Syntax:** `hair <asset-name> <uuid>`

**Description:** Attaches a hair asset to the character.

**Example:**
```
hair long_wavy a1b2c3d4-e5f6-7890-1234-567890abcdef
```

#### `eyes`
**Syntax:** `eyes <asset-name> <uuid>`

**Description:** Attaches an eye asset to the character.

**Example:**
```
eyes HighPolyEyes 2c12f43b-1303-432c-b7ce-d78346baf2e6
```

#### `eyebrows`
**Syntax:** `eyebrows <asset-name> <uuid>`

**Description:** Attaches eyebrow geometry to the character.

#### `eyelashes`
**Syntax:** `eyelashes <asset-name> <uuid>`

**Description:** Attaches eyelash geometry to the character.

#### `teeth`
**Syntax:** `teeth <asset-name> <uuid>`

**Description:** Attaches teeth geometry to the character.

#### `tongue`
**Syntax:** `tongue <asset-name> <uuid>`

**Description:** Attaches tongue geometry to the character.

#### `proxy`
**Syntax:** `proxy <asset-name> <uuid>`

**Description:** Attaches a proxy mesh that can replace or modify the base topology.

**Note:** All asset types follow the same pattern: keyword, asset name, and UUID. The UUID helps identify the specific version of an asset, as names may not be unique across different asset packs.

---

### Material Keywords

#### `skinMaterial`
**Syntax:** `skinMaterial <relative-path-to-material>`

**Description:** Specifies the material file (`.mhmat`) to use for the base mesh skin. The path is relative to the data directory.

**Example:**
```
skinMaterial skins/default.mhmat
skinMaterial skins/caucasian_female.mhmat
```

**Note:** The material file must exist in the `data/skins/` directory structure for the current base mesh.

#### `material`
**Syntax:** `material <asset-name> <uuid> <relative-path-to-material>`

**Description:** Assigns a specific material to an attached asset. This overrides the default material that comes with the asset.

**Example:**
```
material HighPolyEyes 2c12f43b-1303-432c-b7ce-d78346baf2e6 eyes/materials/brown.mhmat
material casual_shirt 7c8f2a1b-4d5e-6789-0123-456789abcdef clothes/materials/red_fabric.mhmat
```

**Parameters:**
1. **asset-name** - Must match an asset name defined earlier in the file
2. **uuid** - Must match the UUID of that asset
3. **relative-path** - Path relative to the asset's base directory

**Note:** Only materials that have been changed from the asset's default are saved in the file.

---

### Skeleton Keywords

#### `skeleton`
**Syntax:** `skeleton <skeleton-filename>`

**Description:** Specifies the skeleton/rig to use for the character. The filename should be just the base name (without path), and the file must exist in the `data/rigs/<basemesh>/` directory.

**Example:**
```
skeleton default.json
skeleton game_rig.json
```

**Optional:** If not specified, no skeleton will be loaded.

---

## Complete Example

Here is a complete example of an `.mhm` file:

```
# MakeHuman2 Model File
# Metadata
version v2.0.1
name Fantasy Warrior
author foo
uuid a1b2c3d4-e5f6-7890-1234-567890abcdef
tags fantasy;male;warrior;medieval

# Body shape modifiers
modifier macrodetails/Gender 0.8
modifier macrodetails/Age 0.4
modifier macrodetails-universal/Muscle 0.75
modifier macrodetails-universal/Weight 0.55
modifier macrodetails-height/Height 0.65
modifier macrodetails-proportions/BodyProportions 0.5
modifier breast/BreastFirmness 0.5
modifier breast/BreastSize 0.5

# Attached assets
eyes HighPolyEyes 2c12f43b-1303-432c-b7ce-d78346baf2e6
hair warrior_short f9e8d7c6-b5a4-3210-9876-543210fedcba
clothes leather_armor 7c8f2a1b-4d5e-6789-0123-456789abcdef
teeth default_teeth 1a2b3c4d-5e6f-7890-abcd-ef1234567890

# Materials
skinMaterial skins/male_caucasian.mhmat
material HighPolyEyes 2c12f43b-1303-432c-b7ce-d78346baf2e6 eyes/materials/blue.mhmat
material leather_armor 7c8f2a1b-4d5e-6789-0123-456789abcdef clothes/materials/brown_leather.mhmat

# Skeleton
skeleton default.mhskel
```

```
# MakeHuman2 Model File
# Metadata
version v2.0.1
name foo
author foo


# Body shape modifiers
modifier bodyshapes/bodyshapes-elvs-fem-full-hourglass 0.64
modifier torso/torso-scale-horiz-decr|incr -0.28
modifier torso/torso-scale-vert-decr|incr -0.1
modifier torso/torso-scale-depth-decr|incr 0.21
modifier measure/measure-shoulder-dist-decr|incr -0.3
modifier measure/measure-napetowaist-dist-decr|incr 0.08
modifier measure/measure-waisttohip-dist-decr|incr -0.04
modifier measure/measure-hips-circ-decr|incr -0.6
modifier custom/macro_muscle/female-young-minmuscle-maxweight 0.17


proxy female_generic 84b1a384-1af0-4126-9030-222edb78b318
eyebrows eyebrow009 55b3b52a-379e-426d-b320-562ed57202b3
eyelashes Eyelashes01 d533836f-13ad-4836-8b65-051108253cd2
clothes shoes05 8ef14ff4-8547-419c-a495-17bab919568b
eyes HighPolyEyes 2c12f43b-1303-432c-b7ce-d78346baf2e6
clothes female_casualsuit01 a8b0e841-144b-4f03-8b9e-ea8fdce8c863
clothes fedora 566dbd52-71d1-4d76-a799-0474a5a384db
teeth Teeth_Base 0a5ec82a-0d5f-43ce-a765-8f076f95692d
tongue tongue01 52ad91a3-caad-4e50-8c38-67634e5e789c
hair bob02 cd7840cf-b340-49e9-a505-8a5a22a86db2
skinMaterial skins/default.mhmat
material HighPolyEyes 2c12f43b-1303-432c-b7ce-d78346baf2e6 eyes/materials/brown.mhmat
skeleton default.mhskel

```

---

## File Loading Process

When MakeHuman2 loads an `.mhm` file, it follows this process:

1. **Parse metadata** - Reads version, name, author, uuid, and tags
2. **Resolve asset paths** - Matches asset names and UUIDs to actual files in the data directory
3. **Load skin material** - Applies the base mesh material
4. **Attach assets** - Loads and attaches each asset (clothes, hair, etc.) in order
5. **Apply materials** - Applies custom materials to assets if specified
6. **Reset and apply modifiers** - Resets all targets to default, then applies each modifier
7. **Load skeleton** - If specified, loads and applies the skeleton/rig
8. **Recalculate pose skeleton** - Updates skeleton based on modified geometry

---

## File Saving Process

When saving an `.mhm` file, MakeHuman2:

1. **Writes header** - Outputs version, name, author from current character
2. **Writes metadata** - Includes UUID and tags if present
3. **Writes modifiers** - Only saves modifiers that differ from default values
4. **Writes assets** - Lists all attached assets with their names and UUIDs
5. **Writes skin material** - Includes the base mesh material path
6. **Writes custom materials** - Only saves materials that have been changed from defaults
7. **Writes skeleton** - Includes skeleton filename if a rig is applied

---

## Path Handling

All file paths in `.mhm` files are stored in **Unix format** (forward slashes) and are **relative paths** from the MakeHuman2 data directory. This ensures portability across different operating systems and installation locations.

### Path Resolution

The program resolves paths in this order:
1. User data directory (`~/.makehuman2/data/`)
2. System data directory (`<install-dir>/data/`)
3. For assets, searches in base-mesh-specific folders (e.g., `data/clothes/hm08/`)

---

## UUID Matching

Assets are matched using both name and UUID. If an exact UUID match isn't found, the system will fall back to matching by name alone, but will log a warning. This allows for some flexibility when assets are updated or modified.

---

## Comments

Lines beginning with `#` are treated as comments and ignored during parsing. This allows for documentation within the file.

```
# This is a comment
# Comments can explain character design choices
```

---

## Compatibility Notes

- The `.mhm` format uses UTF-8 encoding with error tolerance
- Empty lines are ignored
- Unknown keywords are logged but don't cause errors
- Missing assets are logged but don't prevent file loading
- Base mesh must be loaded before an `.mhm` file can be loaded

---

## Relationship with base.json

Each base mesh has an associated `base.json` configuration file (located at `data/base/<mesh-name>/base.json`) that defines the capabilities and constraints for that mesh. The `.mhm` file must work within these constraints:

### Equipment Types
The `equipment` array in `base.json` determines which asset types can be attached. For example:
- If `base.json` lists `["clothes", "hair", "eyes"]`, then only these asset types can be used
- Attempting to load an `.mhm` file with asset types not listed (e.g., `teeth`, `tongue`) will result in those assets being ignored

### Default Values
The `modifier-presets` section in `base.json` defines default modifier values:
- These defaults are applied when the base mesh loads
- `.mhm` file modifiers override these defaults
- If a modifier is not in the `.mhm` file, the `base.json` preset value is used

### Default Model
The `mhm` field in `base.json` can specify a default `.mhm` file to load:
- This provides a starting character when the base mesh is loaded
- Command-line specified `.mhm` files override this default
- If no `.mhm` is specified anywhere, only the `modifier-presets` are applied

### Target Validation
The `target-opposites` in `base.json` defines valid target patterns:
- `.mhm` modifier paths must match available targets for the base mesh
- Missing targets are logged but don't prevent loading

**Example Relationship:**

**base.json:**
```json
{
    "equipment": ["clothes", "hair", "eyes"],
    "mhm": "base.mhm",
    "modifier-presets": {
        "macrodetails/Gender": 0.5,
        "macrodetails/Age": 0.5
    }
}
```

**compatible.mhm:**
```
# This .mhm file works with the above base.json
modifier macrodetails/Gender 0.7
modifier macrodetails/Age 0.3
clothes shirt_basic abc123
hair long_hair def456
eyes default_eyes ghi789
# teeth would be ignored since it's not in equipment list
```

**See also:** [`base-json-format.md`](base-json-format.md) for complete `base.json` documentation.

---

## Related File Formats

- **`base.json`** - Base mesh configuration file that defines capabilities and constraints (see above)
- **`.mhmat`** - Material definition files referenced by `skinMaterial` and `material` keywords
- **`.obj`** - 3D mesh files for base meshes and assets
- **`.json`** (skeleton files) - Skeleton/rig definition files referenced by `skeleton` keyword (e.g., `.mhskel`)
- **`.target`** - Morphing target files referenced by `modifier` keyword

---

## Technical Implementation

The `.mhm` file format is implemented in:
- **`core/baseobj.py`** - Contains `loadMHMFile()` and `saveMHMFile()` methods
- **`gui/mainwindow.py`** - Provides UI callbacks for loading/saving
- **`makehuman.py`** - Handles command-line model loading

---

## Best Practices

1. **Use meaningful names** - Character names help identify files in asset browsers
2. **Add descriptive tags** - Tags improve searchability in large character libraries
3. **Include author information** - Credits help track character origins
4. **Use UUIDs consistently** - Generate and maintain unique UUIDs for shared characters
5. **Keep relative paths** - Don't use absolute paths; keep files portable
6. **Only modify necessary modifiers** - The file only saves non-default values for efficiency

---

## Error Handling

The MakeHuman2 loader is designed to be tolerant:
- Missing assets generate warnings but don't halt loading
- Unknown keywords are logged but skipped
- File I/O errors return descriptive error messages
- UUID mismatches fall back to name-only matching

This robustness ensures that partially broken files can still be loaded and potentially recovered.

