---
title: "modelling.json"
description: "Target Configuration file"
---

## modelling.json
# Target Configuration File

## Overview

The `modelling.json` file defines the properties, behavior, and UI configuration of individual modeling targets in MakeHuman 2. Each entry in this file corresponds to a morph target or set of targets that can modify the character's appearance through sliders in the modeling interface.

This file works in tandem with `target_cat.json`, which defines the category hierarchy. While `target_cat.json` organizes targets into categories, `modelling.json` defines what each target actually does and how it behaves.

## File Format

`modelling.json` is a standard JSON file with a flat structure where each key represents a unique target identifier:

```json
{
  "TargetName1": { /* target properties */ },
  "TargetName2": { /* target properties */ },
  "TargetName3": { /* target properties */ }
}
```

## File Locations

### System Files (Read-Only)

```
<SystemDir>/data/target/<basename>/modelling.json
```

Where `<basename>` is the base mesh identifier (e.g., `hm08`, `mh2bot`)

### User Files (Auto-Generated)

```
<UserDataDir>/data/target/modelling.json
```

Automatically generated when user targets are added to the target directory.

### Constant Targets (Manual Override)

```
<UserDataDir>/data/contarget/modelling.json
```

For advanced users creating completely custom base meshes. This replaces the system file entirely.

## File Loading Order

1. **Check for constant targets:** `<UserDataDir>/data/contarget/modelling.json`
2. **Fall back to system:** `<SystemDir>/data/target/<basename>/modelling.json`
3. **Append user additions:** `<UserDataDir>/data/target/modelling.json` (merged with system)

If `contarget/modelling.json` exists, the system file is ignored. Otherwise, system and user files are merged, with user entries added to or overriding system entries.

## Target Entry Structure

Each target entry is a JSON object with various properties that define its behavior:

```json
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
  "barycentric_diffuse": [[r, g, b], [r, g, b], [r, g, b]]
}
```

## Property Reference

### Basic Properties

#### `group` (string, required)

Specifies the category and subcategory for this target, separated by a pipe character.

**Format:** `"category|subcategory"`

**Example:**
```json
"group": "face|nose size"
"group": "main|macro"
"group": "torso|stomach"
```

The category must match an entry in `target_cat.json` to appear in the UI.

#### `name` (string, optional)

Human-readable display name shown in the UI slider label.

**If omitted:** Generated from the entry key by:
- Removing the category prefix
- Replacing hyphens with spaces
- Capitalizing appropriately

**Example:**
```json
"Nose Width": {
  "name": "width",  // Displayed as "width" in UI
  "group": "face|nose size"
}
```

#### `tip` (string, optional)

Tooltip text displayed when hovering over the slider. Can include HTML tags for formatting.

**Example:**
```json
"tip": "Gender, minimum value is female, maximum value is male."
"tip": "Age of the human (range from 1 year to 90 years old,<br>with center position 25 years)."
```

#### `default` (number, optional, default: 0.5)

Initial slider value when loading a new character.

**Range:** 0.0 to 1.0

**Note:** Internally stored and used as 0-100, so 0.5 becomes 50.

**Example:**
```json
"default": 0.0   // Slider starts at minimum
"default": 0.5   // Slider starts at center (most common)
"default": 1.0   // Slider starts at maximum
```

### Target Files

#### `incr` (string, optional)

Path to the target file that increases this feature.

**Format:** Relative path from target directory, without `.target` extension

**Example:**
```json
"incr": "armslegs/l-hand-fingers-distance"
"incr": "head/head-oval"
"incr": "nose/nose-width-incr"
```

#### `decr` (string, optional)

Path to the target file that decreases this feature.

**Used for:** Bidirectional/dual targets where a feature can increase or decrease

**Example:**
```json
"Nose Width": {
  "incr": "nose/nose-width-incr",
  "decr": "nose/nose-width-decr"
}
```

**Behavior:**
- If both `incr` and `decr` exist: Creates a dual slider with negative and positive ranges
- If only `incr` exists: Creates a unidirectional slider (0 to max)
- If only `decr` exists: Creates a unidirectional slider (0 to max) using decr target

### Visual Elements

#### `icon` (string, optional)

Filename of the icon image to display alongside the slider.

**Location:** `data/target/<basename>/icons/` or user icon directory

**Format:** PNG image (typically 128x128 or 256x256 pixels)

**Example:**
```json
"icon": "head-oval.png"
"icon": "breastsize.png"
"icon": "nose-nostril-flare.png"
```

**Auto-detection for user targets:** If an icon file matches the pattern `<folder>-<targetname>.png`, it's automatically linked.

### Symmetry

#### `sym` (string, optional)

Name of the symmetric counterpart target for left/right symmetry features.

**Used for:** Features that exist on both sides of the body

**Example:**
```json
"l-eye-height": {
  "group": "face|left eye",
  "sym": "r-eye-height",
  "incr": "eyes/l-eye-height-incr",
  "decr": "eyes/l-eye-height-decr"
},
"r-eye-height": {
  "group": "face|right eye",
  "sym": "l-eye-height",
  "incr": "eyes/r-eye-height-incr",
  "decr": "eyes/r-eye-height-decr"
}
```

**Behavior:** When symmetry mode is enabled in the UI, moving one slider automatically moves its symmetric counterpart.

**Legacy properties:**
- `rsym`: Right-side symmetry (being phased out)
- `lsym`: Left-side symmetry (being phased out)

Use `sym` for all new targets.

### Measurement Integration

#### `measure` (string, optional)

Name of a body measurement to display and update alongside the slider.

**Must match:** A measurement defined in `base.json`

**Common measurements:** `"neck"`, `"upperarm"`, `"hips"`, `"chest"`, `"waist"`

**Example:**
```json
"Neck Circumference": {
  "group": "measure|neck",
  "measure": "neck",
  "incr": "measure/neck-circ-incr",
  "decr": "measure/neck-circ-decr"
}
```

**Behavior:** As the slider changes, the specified measurement updates in real-time, showing the character's current dimensions in that area.

### Display Formula

#### `display` (object, optional)

Configures a custom text display that shows a calculated value based on the slider position.

**Properties:**

- **`slot`** (integer): Display slot number (1-4) for positioning in the UI
- **`text`** (string): Format string with `{}` placeholder for the calculated value
- **`formula`** (string): Python expression to calculate the display value

**Available variables in formula:**

- `val`: Current slider value (0.0 to 1.0)

**Can use:** Any Python math operations, conditionals, string operations

**Example 1 - Simple Age Display:**
```json
"display": {
  "slot": 2,
  "text": "Age: {}",
  "formula": "round(1 + val / (0.5 / 24)) if val < 0.5 else round(25 + (val - 0.5) / (0.5 / 65))"
}
```

Result: Shows "Age: 1" at 0.0, "Age: 25" at 0.5, "Age: 90" at 1.0

**Example 2 - Percentage Display:**
```json
"display": {
  "slot": 3,
  "text": "Muscle: {}%",
  "formula": "round(val * 100, 2)"
}
```

Result: Shows "Muscle: 0%" at 0.0, "Muscle: 50%" at 0.5, "Muscle: 100%" at 1.0

**Example 3 - Conditional Text:**
```json
"display": {
  "slot": 1,
  "text": "{}",
  "formula": "'neutral' if 0.49 < val < 0.51 else 'male' if val > 0.99 else 'female' if val < 0.01 else str(round(100.0 - val * 100.0)) + '% female, ' + str(round(val * 100.0)) + '% male'"
}
```

Result: Shows text descriptions like "female", "neutral", "male", or percentages like "30% female, 70% male"

### Macro Targets

Macro targets are complex targets that apply multiple underlying morphs based on calculations involving other target values and macro definitions.

#### `macro` (string, optional)

Path to the macro definition that controls this target's behavior.

**Format:** `"folder/MacroName"` (without extension)

**Common folders:**
- `macrodetails/` - Core macro definitions (Gender, Age, etc.)
- `macrodetails-universal/` - Universal macros (Muscle, Weight)
- `macrodetails-height/` - Height-related macros
- `macrodetails-proportions/` - Proportion macros
- `breast/` - Breast-related macros

**Example:**
```json
"Gender": {
  "group": "main|macro",
  "macro": "macrodetails/Gender",
  "default": 0.5
}
```

**How it works:** The macro system loads a definition from `macro.json` that specifies which underlying targets to apply and at what intensities based on the slider value and other factors.

#### `macro_influence` (array of integers, optional)

List of macro component indices that this target influences.

**Values:** Array of integers from 0 to 4, each representing a macro component:
- **0**: Often ethnicity/phenotype
- **1**: Often age/maturity related
- **2**: Often proportion related
- **3**: Often height/scale related
- **4**: Often gender/sexual characteristics

**Example:**
```json
"Gender": {
  "macro": "macrodetails/Gender",
  "macro_influence": [0, 1, 2, 3, 4]  // Affects all components
}
```

```json
"Height": {
  "macro": "macrodetails-height/Height",
  "macro_influence": [3]  // Only affects height component
}
```

```json
"Muscle": {
  "macro": "macrodetails-universal/Muscle",
  "macro_influence": [1, 2, 3, 4]  // Affects all except ethnicity
}
```

**Purpose:** The macro influence system allows targets to interact and produce combined effects. When multiple macro targets are changed, their influences on shared components are calculated together to produce the final character shape.

### Barycentric Targets

Barycentric targets use three-way interpolation between three different states, creating smooth transitions in a triangular coordinate system.

#### `barycentric` (array of 3 strings, optional)

Three target paths defining the corners of a barycentric triangle.

**Format:** `["path/target1", "path/target2", "path/target3"]`

**Example:**
```json
"Phenotype": {
  "group": "main|macro",
  "barycentric": [
    "macrodetails/African",
    "macrodetails/Asian",
    "macrodetails/Caucasian"
  ],
  "macro_influence": [0]
}
```

**UI Behavior:** Creates a triangular slider interface where:
- Each corner represents 100% of one target
- The center represents 33.33% of each target
- Any point inside represents a weighted blend of all three targets

#### `barycentric_diffuse` (array of 3 arrays, optional)

Three color values (RGB) or texture filenames to blend along with the barycentric targets.

**Format 1 - RGB Colors:**
```json
"barycentric_diffuse": [
  [0.207, 0.113, 0.066],  // African skin tone
  [0.721, 0.568, 0.431],  // Asian skin tone
  [0.843, 0.639, 0.517]   // Caucasian skin tone
]
```

**Format 2 - Texture Files:**
```json
"barycentric_diffuse": [
  "skins/african.png",
  "skins/asian.png",
  "skins/caucasian.png"
]
```

**Behavior:** As the barycentric slider changes, the diffuse color/texture smoothly transitions between the three specified values, providing visual feedback and realistic skin tone variation.

### User Target Properties

#### `user` (integer, value: 1)

Automatically added to user-generated target entries.

**Purpose:** Indicates this is a custom user target

**Effect:** Causes the system to prepend `"custom/"` to target file paths during loading

**Example (auto-generated):**
```json
"Custom MyMorph": {
  "user": 1,
  "group": "user|custom",
  "incr": "custom/mymorph-incr",
  "icon": "custom-mymorph.png"
}
```

**Note:** You don't add this manually; the auto-generation system adds it when scanning user targets.

## Target Type Examples

### 1. Simple Unidirectional Target

A basic target that only increases a feature (no decrease).

```json
"Head Oval": {
  "name": "Oval Shape",
  "group": "face|head shape",
  "icon": "head-oval.png",
  "incr": "head/head-oval"
}
```

**UI:** Single slider from 0 (no effect) to 100 (maximum effect)

### 2. Dual Bidirectional Target

A target that can increase or decrease a feature.

```json
"Nose Width": {
  "group": "face|nose size",
  "name": "width",
  "tip": "Makes the nose wider or narrower",
  "incr": "nose/nose-width-incr",
  "decr": "nose/nose-width-decr",
  "default": 0.5,
  "icon": "nose-width.png"
}
```

**UI:** Dual slider with:
- Left side (0-50): Applies decr target
- Center (50): Neutral, no effect
- Right side (50-100): Applies incr target

### 3. Symmetric Target Pair

Two related targets that can move together when symmetry is enabled.

```json
"l-eye-height": {
  "group": "face|left eye",
  "name": "eye height",
  "tip": "Raise or lower the left eye",
  "incr": "eyes/l-eye-height-incr",
  "decr": "eyes/l-eye-height-decr",
  "sym": "r-eye-height",
  "default": 0.5,
  "icon": "eye-height.png"
},
"r-eye-height": {
  "group": "face|right eye",
  "name": "eye height",
  "tip": "Raise or lower the right eye",
  "incr": "eyes/r-eye-height-incr",
  "decr": "eyes/r-eye-height-decr",
  "sym": "l-eye-height",
  "default": 0.5,
  "icon": "eye-height.png"
}
```

**UI:** Two separate sliders that can be:
- Adjusted independently (normal mode)
- Adjusted together (symmetry mode enabled)

### 4. Macro Target with Display Formula

A complex target controlling multiple underlying morphs with custom display.

```json
"Age": {
  "group": "main|macro",
  "tip": "Age of the human (range from 1 year to 90 years old, with center position 25 years).",
  "macro": "macrodetails/Age",
  "macro_influence": [0, 1, 2, 3, 4],
  "default": 0.5,
  "display": {
    "slot": 2,
    "text": "Age: {}",
    "formula": "round(1 + val / (0.5 / 24)) if val < 0.5 else round(25 + (val - 0.5) / (0.5 / 65))"
  }
}
```

**UI:** Slider with text display showing calculated age in years (1 to 90)

### 5. Measurement Target

A target that updates a body measurement.

```json
"Neck Circumference": {
  "group": "measure|neck",
  "name": "circumference",
  "tip": "Adjust neck thickness",
  "incr": "measure/neck-circ-incr",
  "decr": "measure/neck-circ-decr",
  "measure": "neck",
  "default": 0.5,
  "icon": "neck-circ.png"
}
```

**UI:** Slider with live measurement update showing neck circumference in cm or inches

### 6. Barycentric Target

A three-way interpolation target.

```json
"Phenotype": {
  "group": "main|macro",
  "tip": "Ethnic characteristics",
  "barycentric": [
    "macrodetails/African",
    "macrodetails/Asian",
    "macrodetails/Caucasian"
  ],
  "barycentric_diffuse": [
    [0.207, 0.113, 0.066],
    [0.721, 0.568, 0.431],
    [0.843, 0.639, 0.517]
  ],
  "macro_influence": [0]
}
```

**UI:** Triangular map slider where each corner represents one ethnicity, with smooth blending between all three

### 7. User-Generated Target (Auto-Created)

Automatically generated when adding custom targets.

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

**Created from files:**
```
~/makehuman/v1py3/data/target/
  custom/
    myfeature-incr.target
    myfeature-decr.target
  icons/
    custom-myfeature.png
```

## Automatic Generation for User Targets

### How It Works

When you add `.target` files to the user target directory, MakeHuman automatically generates entries in `modelling.json`:

1. **Scans** `~/makehuman/v1py3/data/target/` for `.target` files
2. **Detects** folder structure (subfolders become subcategories)
3. **Identifies** dual target pairs (matching `-incr` and `-decr` suffixes)
4. **Links** icon files (if they follow naming conventions)
5. **Generates** appropriate JSON entries
6. **Writes** `modelling.json` in the user target directory

### Dual Target Detection

**File naming convention:**
```
features/custom-feature-incr.target
features/custom-feature-decr.target
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

### Single Target

**File:**
```
morphs/big-ears.target
```

**Generated entry:**
```json
"Morphs Big Ears": {
  "user": 1,
  "name": "big ears",
  "group": "user|morphs",
  "incr": "morphs/big-ears"
}
```

### Icon Linking

Icons are automatically linked if they match the pattern:

**Pattern 1:** `<subfolder>-<targetname>.png`
```
target/custom/my-morph-incr.target
icons/custom-my-morph.png  ← Auto-linked
```

**Pattern 2:** `<targetname>.png` (if no subfolder)
```
target/simple-morph.target
icons/simple-morph.png  ← Auto-linked
```

**Note:** Icon should not include `-incr` or `-decr` suffix for dual targets.

### Regeneration Trigger

The system regenerates `modelling.json` when:
- A `.target` file is added, removed, or modified
- An icon file is added or removed
- The modification time of any file is newer than the existing `modelling.json`

This ensures the JSON file stays synchronized with actual target files.

## Creating Manual Entries

### For System Targets

Edit the system `modelling.json` file (requires system access):
```
<SystemDir>/data/target/<basename>/modelling.json
```

**Typical workflow:**
1. Add target files to appropriate directory
2. Create icon (optional)
3. Add entry to `modelling.json`
4. Add category to `target_cat.json` if needed

### For Custom Base Meshes

Use the `contarget` directory for complete control:

**Location:** `~/makehuman/v1py3/data/contarget/modelling.json`

**Example:**
```json
{
  "CustomHead": {
    "group": "custom|head",
    "name": "Custom Head Shape",
    "tip": "My custom head morph",
    "incr": "custom-head-incr",
    "default": 0.5,
    "icon": "custom-head.png"
  },
  "CustomBody": {
    "group": "custom|body",
    "name": "Custom Body Shape",
    "incr": "custom-body-incr",
    "decr": "custom-body-decr"
  }
}
```

**Important:** Files in `contarget` completely replace the system files. This is for creating entirely new base meshes with custom target systems.

## Integration with Other Systems

### Target Files

`modelling.json` references target files:
- **Format:** Binary `.npz` (compiled) or ASCII `.target` (source)
- **Location:** Same directory structure as `modelling.json`
- **See:** [target.md](target.md) for target file format

### Category System

`modelling.json` entries must reference categories defined in `target_cat.json`:
- **`group` field:** Must match a category identifier
- **Format:** `"category|subcategory"`
- **See:** [target-categories.md](target-categories.md) for category system

### Macro System

Macro targets reference definitions in `macro.json`:
- **`macro` field:** Path to macro definition
- **`macro_influence` field:** Which components are affected
- **See:** [macro.json.md](macro.json.md) for macro system

### Base Configuration

Some properties interact with `base.json`:
- **`measure` field:** Must reference measurements defined in `base.json`
- **Default values:** May be overridden by `modifier-presets` in `base.json`
- **See:** [base-json-format.md](base-json-format.md) for base configuration

### Character Files

Target values are saved in `.mhm` character files:
- **Format:** `modifier <target-key> <value>`
- **Values:** Stored as 0-100 internally (0.0-1.0 in JSON × 100)
- **See:** [mhm.md](mhm.md) for character file format

## Example

[modelling.json](modelling.json)

