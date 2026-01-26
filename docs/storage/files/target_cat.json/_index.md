---
title: "target_cat.json"
description: "MakeHuman Target Category Definition file"
---

## target_cat.json

# Target Categories Configuration

## Overview
The MakeHuman2 target system uses two complementary JSON configuration files to organize and define morphing targets:

- **`target_cat.json`** - Defines the category hierarchy and UI organization of targets
- **`modelling.json`** - Defines individual target properties, behaviors, and slider configurations

Together, these files control how targets appear in the user interface and how they behave when applied to the character model.

## File Locations

### System Files (Read-Only)
```
<SystemDir>/data/target/<basename>/target_cat.json
<SystemDir>/data/target/<basename>/modelling.json
```
Where `<basename>` is the base mesh identifier (e.g., `hm08`, `mh2bot`)

### User Files (Auto-Generated for Custom Targets)
```
<UserDataDir>/data/target/target_cat.json        # Auto-generated
<UserDataDir>/data/target/modelling.json         # Auto-generated
```

### Constant Targets (Manual Override)
```
<UserDataDir>/data/contarget/target_cat.json    # Manual
<UserDataDir>/data/contarget/modelling.json     # Manual
```

**Important:** The `contarget` folder is used for constant targets when you want to create a completely custom base mesh with its own target system. If present, the system target folder is ignored. Use this for advanced customization only.

## File Loading Order

1. **Check for constant targets:** `<UserDataDir>/data/contarget/target_cat.json`
2. **Fall back to system:** `<SystemDir>data/target/<basename>/target_cat.json`
3. **Load user additions:** `<UserDataDir>/data/target/target_cat.json` (appended to system)

The same order applies to `modelling.json`.

---

## target_cat.json

### Purpose

`target_cat.json` defines the hierarchical structure of target categories displayed in the modeling UI. It organizes targets into groups and subgroups, making them easy to browse and discover.

### File Structure

```json
{
  "CategoryName": {
    "group": "group-identifier",
    "items": [
      {"title": "Display Name", "cat": "internal-category-name"}
    ]
  }
}
```

### Example

```json
{
  "Main": {
    "group": "main",
    "items": [
      {"title": "Macro", "cat": "macro"}
    ]
  },
  "Face": {
    "group": "face",
    "items": [
      {"title": "Head shape", "cat": "head shape"},
      {"title": "Nose size", "cat": "nose size"},
      {"title": "Nose features", "cat": "nose features"}
    ]
  },
  "Torso": {
    "group": "torso",
    "items": [
      {"title": "Shape", "cat": "shape"},
      {"title": "Hip", "cat": "hip"},
      {"title": "Stomach", "cat": "stomach"}
    ]
  }
}
```

### Field Definitions

#### Top-Level Categories

Each top-level key (e.g., `"Main"`, `"Face"`) represents a major category tab or section in the UI.

**Properties:**
- **`group`** (string): Internal group identifier used to link targets to this category
- **`items`** (array): Array of subcategory objects

#### Items Array

Each item represents a subcategory that can be selected to show related targets.

**Properties:**
- **`title`** (string): Display name shown in the UI
- **`cat`** (string): Internal category identifier that matches the `group` field in `modelling.json`

### User Target Categories

When user targets are added to `<UserDataDir>/data/target/`, the system automatically:

1. Scans the target folder for `.target` files
2. Organizes them by subfolder structure
3. Generates `target_cat.json` and `modelling.json` in the user target folder
4. Appends a "User" category to the system categories

**Auto-Generated User Category Structure:**
```json
{
  "User": {
    "group": "user",
    "items": [
      {"title": "Subfolder1", "cat": "subfolder1"},
      {"title": "Subfolder2", "cat": "subfolder2"},
      {"title": "Unsorted", "cat": "unsorted"}
    ]
  }
}
```

Targets in subfolders are grouped by folder name; targets in the root target directory go into "Unsorted".

---

## modelling.json

### Purpose

`modelling.json` defines the properties and behavior of individual modeling targets. Each entry corresponds to a target or set of targets that can modify the character's appearance.

### File Structure

```json
{
  "TargetName": {
    "group": "category|subcategory",
    "name": "Display Name",
    "tip": "Tooltip description",
    "incr": "path/to/increase-target",
    "decr": "path/to/decrease-target",
    "default": 0.5,
    "sym": "SymmetricTargetName",
    "icon": "icon-filename.png",
    "measure": "measurement-name",
    "display": {
      "slot": 1,
      "text": "Format: {}",
      "formula": "python expression"
    },
    "macro": "macro/path",
    "macro_influence": [0, 1, 2, 3],
    "barycentric": ["target1", "target2", "target3"],
    "barycentric_diffuse": ["color1.png", "color2.png", "color3.png"]
  }
}
```

### Field Definitions

#### Basic Properties

**`group`** (string, required)
- Category and subcategory separated by pipe (`|`)
- Format: `"category|subcategory"`
- Example: `"face|nose size"` or `"main|macro"`
- Links this target to categories defined in `target_cat.json`

**`name`** (string, optional)
- Human-readable display name for the slider
- If omitted, generated from the key by replacing hyphens with spaces and capitalizing

**`tip`** (string, optional)
- Tooltip text shown when hovering over the slider
- Can include HTML tags like `<br>` for line breaks

**`default`** (number, optional, default: 0.5)
- Initial slider value (0.0 to 1.0 range)
- Value is stored internally as 0-100, so 0.5 becomes 50

#### Simple Targets

**`incr`** (string, optional)
- Path to the target file that increases this feature
- Path is relative to the target directory
- Extension `.target` is omitted
- Example: `"armslegs/l-hand-fingers-distance"`

**`decr`** (string, optional)
- Path to the target file that decreases this feature
- Used for dual/bidirectional targets
- If both `incr` and `decr` exist, creates a slider with negative and positive ranges

**`sym`** (string, optional)
- Name of the symmetric counterpart target
- Used for left/right symmetry features
- Example: For `"l-eye-height"`, sym would be `"r-eye-height"`
- When symmetry mode is enabled, both targets move together

**`icon`** (string, optional)
- Filename of the icon image to display with this target
- Located in `data/target/<basename>/icons/` or `~/makehuman/v1py3/data/target/icons/`
- Format: PNG image
- Auto-detected for user targets if named correctly

#### Measurement Integration

**`measure`** (string, optional)
- Name of a body measurement to display alongside the slider
- Must match a measurement defined in `base.json`
- Example: `"neck"`, `"upperarm"`, `"hips"`
- When present, shows real-time measurement updates as the slider changes

#### Display Formula

**`display`** (object, optional)
- Configures a custom text display for this target's value

**Properties:**
- **`slot`** (integer): Text slot number (1-4) to display the value in the UI
- **`text`** (string): Format string with `{}` placeholder for the calculated value
- **`formula`** (string): Python expression to calculate display value
  - Variable `val` contains the current slider value (0.0-1.0)
  - Can use any Python math or string operations
  - Example: `"round(1 + val / (0.5 /24)) if val < .5 else round(25 + (val-0.5) / (0.5 /65))"`

**Example:**
```json
"display": {
  "slot": 2,
  "text": "Age: {}",
  "formula": "round(1 + val / (0.5 /24)) if val < .5 else round(25 + (val-0.5) / (0.5 /65))"
}
```
This displays "Age: 25" when slider is at 0.5, "Age: 1" at 0.0, "Age: 90" at 1.0.

#### Macro Targets

Macro targets apply multiple underlying targets based on complex calculations involving other target values.

**`macro`** (string, optional)
- Path to macro definition
- Format: `"macrodetails/MacroName"` or `"macrodetails-category/MacroName"`
- Loaded from `macro.json` system
- Example: `"macrodetails/Gender"`, `"macrodetails-universal/Weight"`

**`macro_influence`** (array of integers, optional)
- List of macro component indices this target influences
- References components defined in `macro.json`
- Example: `[0, 1, 2, 3, 4]` means all five base macro components are affected

#### Barycentric Targets

Barycentric targets use three-way interpolation between three different targets, useful for smooth transitions between multiple states.

**`barycentric`** (array of 3 strings, optional)
- Three target paths defining the corners of a barycentric coordinate system
- Creates a triangular slider interface
- Format: `["path/target1", "path/target2", "path/target3"]`
- Each corner represents 100% of one target, 0% of the others
- Interior points blend all three targets

**`barycentric_diffuse`** (array of 3 strings, optional)
- Three texture filenames to blend along with the barycentric targets
- Used to smoothly transition between different skin tones or colors
- Format: `["texture1.png", "texture2.png", "texture3.png"]`
- Textures are blended based on the barycentric weights

**Example:**
```json
"African": {
  "group": "main|macro",
  "barycentric": ["macrodetails/African", "macrodetails/Asian", "macrodetails/Caucasian"],
  "barycentric_diffuse": ["skins/african.png", "skins/asian.png", "skins/caucasian.png"],
  "macro_influence": [4]
}
```

#### User Targets

User-created targets automatically get special properties:

**`user`** (integer, value: 1)
- Automatically added to user-generated entries
- Indicates this is a custom user target
- Causes the pattern search to prepend `"custom/"` to target paths

### Target Types

#### 1. Simple Unidirectional Target

```json
"Head Oval": {
    "name": "Oval Shape",
    "group": "face|head shape",
    "icon": "head-oval.png",
    "incr": "head/head-oval"
}
```

- Single slider from 0 to maximum
- Only increases the feature

#### 2. Dual Bidirectional Target
```json
"Nose Width": {
  "group": "face|nose size",
  "name": "width",
  "incr": "nose/nose-width-incr",
  "decr": "nose/nose-width-decr"
}
```
- Slider with negative and positive ranges
- Can increase or decrease the feature
- Center position (0.5) is neutral

#### 3. Symmetric Target
```json
"l-eye-height": {
  "group": "face|left eye",
  "name": "eye height",
  "incr": "eyes/l-eye-height-incr",
  "decr": "eyes/l-eye-height-decr",
  "sym": "r-eye-height"
}
```
- Paired with another target for symmetry
- When symmetry mode is on, both move together

#### 4. Macro Target
```json
"Gender": {
  "group": "main|macro",
  "tip": "Gender, minimum value is female, maximum value is male.",
  "macro": "macrodetails/Gender",
  "macro_influence": [0, 1, 2, 3, 4],
  "default": 0.5,
  "display": {
    "slot": 1,
    "text": "{}",
    "formula": "'neutral' if 0.49 < val < 0.51 else 'male' if val > 0.99 else 'female' if val < 0.01 else str(round (100.0 - val*100.0)) + '% female, ' + str(round(val*100.0)) + '% male'"
  }
}
```
- Controls multiple underlying targets
- Complex calculation based on macro system
- Often includes custom display formula

#### 5. Barycentric Target
```json
"Race": {
  "group": "main|macro",
  "barycentric": ["macrodetails/African", "macrodetails/Asian", "macrodetails/Caucasian"],
  "barycentric_diffuse": ["african.png", "asian.png", "caucasian.png"],
  "macro_influence": [4]
}
```
- Three-way interpolation
- Triangular UI control
- Can blend textures simultaneously

---

## Automatic Generation for User Targets

### How It Works

The system automatically generates `target_cat.json` and `modelling.json` for user targets by:

1. **Scanning** the user target directory for `.target` files
2. **Detecting** folder structure and icon files
3. **Checking** modification times to determine if regeneration is needed
4. **Identifying** dual targets (matching `-incr` and `-decr` pairs)
5. **Formatting** entries based on naming conventions
6. **Writing** new JSON files if targets or icons have changed

### Dual Target Detection

The system automatically recognizes target pairs:

**File naming convention:**
```
features/custom-feature-incr.target
features/custom-feature-decr.target
features/custom-feature.png          (optional icon)
```

**Generated entry:**
```json
"Features Custom Feature": {
  "user": 1,
  "name": "custom feature",
  "group": "user|features",
  "incr": "features/custom-feature-incr",
  "decr": "features/custom-feature-decr",
  "icon": "features-custom-feature.png"
}
```

### Icon Detection

Icons are automatically detected and linked if:
- Icon file matches pattern: `<subfolder>-<targetname>.png` or `<targetname>.png`
- Icon exists in `icons/` subfolder within the target directory
- For dual targets, icon should not include `-incr` or `-decr` suffix

**Example:**
```
target/
  custom/
    my-morph-incr.target
    my-morph-decr.target
  icons/
    custom-my-morph.png          ← Auto-linked
```

### Modification Time Checking

The system compares modification times to determine if regeneration is needed:

1. Checks `target_cat.json` modification time
2. Checks `modelling.json` modification time
3. Uses the older of the two as the baseline
4. Scans all target files and icon files
5. If any file is newer than the baseline, regenerates both JSON files

This ensures efficient updates - JSON files are only regenerated when targets change.

---

## Creating Custom Target Categories

### For Additional User Targets 

**Just add `.target` files** to the user directory:

```
~/makehuman/v1py3/data/target/
  mystuff/
    cool-morph-incr.target
    cool-morph-decr.target
  icons/
    mystuff-cool-morph.png
```

The system will automatically:
- Create a "User" category
- Add a "Mystuff" subcategory
- Generate appropriate JSON entries
- Link the icon

### For Custom Base Meshes (Advanced)

**Use the `contarget` directory:**

1. Create directory structure:
```
~/makehuman/v1py3/data/contarget/
```

2. Manually create `target_cat.json`:
```json
{
  "Custom Category": {
    "group": "custom",
    "items": [
      {"title": "My Targets", "cat": "mytargets"}
    ]
  }
}
```

3. Manually create `modelling.json`:
```json
{
  "CustomMorph": {
    "group": "custom|mytargets",
    "name": "Custom Morph",
    "incr": "custom-morph-incr",
    "default": 0.5
  }
}
```

4. Add target files to the same directory:
```
contarget/custom-morph-incr.target
```

**Important:** Files in `contarget` replace the system target files entirely. Use this only when creating a completely custom base mesh with its own target system.

---

## Troubleshooting

### Target doesn't appear in UI

**Check:**
- Target file exists in correct location
- `target_cat.json` includes appropriate category
- `modelling.json` has entry with matching `group` field
- Group matches format: `"category|subcategory"`
- Restart MakeHuman if files were added while running

### Auto-generation not working

**Check:**
- User target directory exists: `<UserDataDir>/data/target/`
- Target files have `.target` extension
- File permissions allow writing JSON files
- Check log for error messages during startup

### Dual target not recognized

**Check:**
- Both files exist: `name-incr.target` and `name-decr.target`
- Names match exactly except for `-incr`/`-decr` suffix
- Files are in same directory
- Regenerate by touching one of the target files (update modification time)

### Icon not showing

**Check:**
- Icon is in `icons/` subfolder
- Icon filename matches pattern: `<folder>-<target>.png` or `<target>.png` (without `-incr`/`-decr`)
- Icon is valid PNG format
- Regenerate JSON files by touching a target file

### Symmetry not working

**Check:**
- `sym` field points to valid target name (exact match)
- Both targets exist in `modelling.json`
- Symmetric target has reciprocal `sym` field
- Symmetry mode is enabled in UI

### Measure not updating

**Check:**
- Measurement name matches definition in `base.json`
- Measurement is defined for the current base mesh
- Target actually affects the measured body part

---

## Related Documentation

- **[target.md](target.md)** - Target file format and creation
- **[base-json-format.md](base-json-format.md)** - Base mesh configuration
- **[custom_target.md](custom_target.md)** - Creating custom targets
- **[mhm.md](mhm.md)** - Character file format (includes target values)

---

## Examples

### Complete Simple Target Entry
```json
"Example Simple": {
  "group": "face|nose size",
  "name": "nostril flare",
  "tip": "Makes nostrils wider or narrower",
  "incr": "nose/nostril-flare-incr",
  "decr": "nose/nostril-flare-decr",
  "default": 0.5,
  "icon": "nose-nostril-flare.png"
}
```

### Complete Macro Target Entry
```json
"Body Mass": {
  "group": "main|macro",
  "name": "Body Mass Index",
  "tip": "Overall body mass from thin to heavy",
  "macro": "macrodetails-universal/Weight",
  "macro_influence": [1, 2, 3, 4],
  "default": 0.5,
  "display": {
    "slot": 3,
    "text": "BMI: {}",
    "formula": "round(15 + val * 25, 1)"
  }
}
```

### Complete Measurement Target Entry
```json
"Neck Circumference": {
  "group": "measure|neck",
  "name": "circumference",
  "incr": "measure/neck-circ-incr",
  "decr": "measure/neck-circ-decr",
  "measure": "neck",
  "default": 0.5
}
```

### Auto-Generated User Target Entry
```json
"Custom MyFeature": {
  "user": 1,
  "name": "myfeature",
  "group": "user|custom",
  "incr": "custom/myfeature-incr",
  "decr": "custom/myfeature-decr",
  "icon": "custom-myfeature.png"
}
```

---

## Summary

The `target_cat.json` and `modelling.json` files work together to create a complete target system:

- **`target_cat.json`** provides the organizational structure (categories and UI layout)
- **`modelling.json`** provides the individual target definitions (behavior and properties)

For most users, the automatic generation system handles everything - just add your `.target` files to the user directory and the system creates appropriate JSON entries. For advanced users creating custom base meshes, manual creation of these files in the `contarget` directory provides complete control over the target system.


### Example 

[target_cat.json](target_cat.json)

[modelling.json](modelling.json)
