---
title: "macro.json"
weight: 26
description: "Documentation for macro.json - complex multi-target morphing system"
---

## macro.json
# Macro Target System (macro.json)

## Overview

The `macro.json` file defines MakeHuman2's **macro target system** - a sophisticated mechanism for applying complex combinations of morphing targets based on hierarchical slider interactions. Macro targets allow a single slider (like "Gender" or "Age") to automatically calculate and apply dozens or even hundreds of underlying targets with appropriate weights.

The macro system solves the problem of **interdependent character attributes**: when you change gender, age, muscle, and weight, the system needs to apply different combinations of targets that work together harmoniously. The macro system calculates these combinations automatically.

## File Location

### System Files
```
<SystemDir>/data/target/<basename>/macro.json
```
Where `<basename>` is the base mesh identifier (e.g., `hm08`, `mh2bot`)

### User Override
```
<UserDataDir>/data/contarget/macro.json
```
User macro files replace the system file entirely (not appended).

## Loading Priority

1. **Check user contarget:** `<UserDataDir>/data/contarget/macro.json`
2. **Fall back to system:** `<SystemDir>data/target/<basename>/macro.json`

## File Structure

The `macro.json` file consists of two main sections:

```json
{
  "components": {
    "component-name": {
      "values": ["value1", "value2", "value3"],
      "pattern": "target/path/prefix",
      "steps": [0.0, 0.5, 1.0],
      "sum": ["Slider1", "Slider2", "Slider3"]
    }
  },
  "macrodef": [
    {
      "name": "macro-name",
      "comp": ["component1", "component2"],
      "folder": "target_folder",
      "targets": [
        {"name": "calculated-name", "t": "actual-target-file"}
      ]
    }
  ]
}
```

---

## Components Section

The `components` object defines **reusable building blocks** that describe how slider values map to target naming patterns. Each component represents a character dimension (gender, age, muscle, etc.).

### Component Structure

```json
"component-name": {
  "values": ["label1", "label2", "label3"],
  "pattern": "target-path-prefix",
  "steps": [0.0, 0.5, 1.0],
  "sum": ["TargetName1", "TargetName2"]
}
```

### Field Definitions

#### `values` (array of strings, required)
- List of naming labels for this component's states
- Used to construct target filenames
- Number of values determines how many states the component has
- Examples: `["female", "male"]`, `["minmuscle", "averagemuscle", "maxmuscle"]`

#### `pattern` (string, required)
- Path prefix to the target controlling this component
- Must match a target defined in `modelling.json`
- Used to read current slider values
- Examples: `"macrodetails/Gender"`, `"macrodetails-universal/Muscle"`

#### `steps` (array of numbers, optional)
- Defines breakpoints on the slider (0.0 to 1.0 range)
- Used for **stepped interpolation** between discrete states
- Array length must equal `values` array length
- When present, creates interpolation between adjacent steps
- Example: `[0.0, 0.5, 1.0]` means value1 at 0%, value2 at 50%, value3 at 100%

#### `sum` (array of strings, optional)
- Used for **barycentric (3-way) sliders** instead of stepped sliders
- Lists the target names whose barycentric values sum to determine weights
- Mutually exclusive with `steps` - use one or the other
- Must have exactly 3 entries for barycentric interpolation
- Example: `["African", "Asian", "Caucasian"]`

### Component Types

#### 1. Stepped Component (Two States)

**Example: Gender**
```json
"gender": {
  "values": ["female", "male"],
  "steps": [0.0, 1.0],
  "pattern": "macrodetails/Gender"
}
```

**How it works:**
- Slider at 0.0 → 100% `female`, 0% `male`
- Slider at 0.5 → 50% `female`, 50% `male`
- Slider at 1.0 → 0% `female`, 100% `male`
- Linear interpolation between the two states

#### 2. Stepped Component (Three States)

**Example: Muscle**
```json
"muscle": {
  "values": ["minmuscle", "averagemuscle", "maxmuscle"],
  "steps": [0.0, 0.5, 1.0],
  "pattern": "macrodetails-universal/Muscle"
}
```

**How it works:**
- Slider 0.0 to 0.5 → interpolate between `minmuscle` and `averagemuscle`
- Slider 0.5 to 1.0 → interpolate between `averagemuscle` and `maxmuscle`
- At exactly 0.5 → 100% `averagemuscle`

#### 3. Stepped Component (Four States)

**Example: Age**
```json
"age": {
  "values": ["baby", "child", "young", "old"],
  "steps": [0.0, 0.1875, 0.5, 1.0],
  "pattern": "macrodetails/Age"
}
```

**How it works:**
- Four age ranges with different step sizes
- 0.0 to 0.1875 → baby to child transition
- 0.1875 to 0.5 → child to young transition
- 0.5 to 1.0 → young to old transition
- Non-uniform steps allow different aging speeds

#### 4. Barycentric Component (Sum-Based)

**Example: Phenotype**
```json
"phenotype": {
  "values": ["african", "asian", "caucasian"],
  "pattern": "macrodetails/",
  "sum": ["African", "Asian", "Caucasian"]
}
```

**How it works:**
- Reads three barycentric slider values that sum to 1.0
- Each corner can be 0% to 100%
- Pattern + sum entry = full target path (e.g., `"macrodetails/African"`)
- All three values are read simultaneously
- Example: 60% african, 20% asian, 20% caucasian

### Standard Components

Most base meshes use these standard components:

| Component | Values | Type | Purpose |
|-----------|--------|------|---------|
| `gender` | female, male | Stepped (2) | Gender morphing |
| `age` | baby, child, young, old | Stepped (4) | Age progression |
| `muscle` | minmuscle, averagemuscle, maxmuscle | Stepped (3) | Muscle definition |
| `weight` | minweight, averageweight, maxweight | Stepped (3) | Body mass |
| `phenotype` | african, asian, caucasian | Barycentric | Ethnic features |
| `proportion` | uncommon, none, ideal | Stepped (3) | Beauty proportions |
| `height` | minheight, none, maxheight | Stepped (3) | Height adjustment |
| `cupsize` | mincup, averagecup, maxcup | Stepped (3) | Breast size (female) |
| `firmness` | minfirmness, averagefirmness, maxfirmness | Stepped (3) | Breast firmness |

---

## Macrodef Section

The `macrodef` array defines **macro target definitions** - collections of targets that represent every possible combination of component states.

### Macrodef Structure

```json
{
  "name": "descriptive-macro-name",
  "comp": ["component1", "component2", "component3"],
  "folder": "target-subfolder",
  "targets": [
    {"name": "component1-component2-component3", "t": "actual-file-name"}
  ]
}
```

### Field Definitions

#### `name` (string, required)
- Descriptive name for this macro definition
- Used for logging and debugging
- Should indicate which components are involved
- Example: `"phenotype-gender-age"`, `"gender-age-muscle-weight"`

#### `comp` (array of strings, required)
- List of component names used in this macro
- Must reference components defined in the `components` section
- Order matters - determines how target names are constructed
- Example: `["gender", "age", "muscle"]`

#### `folder` (string, required)
- Subdirectory containing the target files for this macro
- Relative to the target directory
- Example: `"macro_age"`, `"macro_muscle"`, `"macro_breast"`

#### `targets` (array of objects, required)
- Complete list of all possible target combinations
- Each entry maps a calculated name to an actual target file
- Array is cleared after processing to save memory

### Target Entry Structure

```json
{"name": "calculated-target-name", "t": "actual-target-file-name"}
```

**Fields:**

- **`name`** (string): Calculated name based on component value combinations
  - Format: `"value1-value2-value3-..."`
  - Joined by hyphens in the order specified in `comp`
  - Example: `"female-young-maxmuscle-minweight"`

- **`t`** (string or null): Actual target filename (without `.target` extension)
  - May be `null` if no target exists for this combination
  - May differ from `name` if targets are reused or aliased
  - Path relative to the `folder`
  - Example: `"female-young-maxmuscle-minweight"` or `null`

### Target Mapping (targetlink)

After loading, the system creates an internal **`targetlink`** dictionary that maps calculated names to full target paths:

```json
"targetlink": {
  "female-young-maxmuscle-minweight": "macro_muscle/female-young-maxmuscle-minweight",
  "male-child-averagemuscle-averageweight": null
}
```

This mapping is used during macro calculation to find the actual target files to apply.

---

## How Macro Calculation Works

### Conceptual Overview

When a macro slider changes (like Gender, Age, or Weight), the system:

1. **Reads current values** of all relevant sliders
2. **Calculates component weights** based on slider positions
3. **Generates target combinations** by crossing all component values
4. **Applies interpolation weights** for smooth transitions
5. **Maps calculated names** to actual target files
6. **Applies targets** with appropriate factors to the mesh

### Step-by-Step Process

#### Step 1: Identify Influenced Macros

Each macro target in `modelling.json` has a `macro_influence` field:

```json
"Gender": {
  "macro": "macrodetails/Gender",
  "macro_influence": [0, 1, 2, 3, 4]
}
```

This indicates which macrodef entries (by index) are affected by this slider.

#### Step 2: Read Component Values

For each macro definition, read the current slider values for all components:

```python
# For macrodef with comp: ["gender", "age", "muscle"]
gender_value = slider["macrodetails/Gender"].value / 100  # 0.0 to 1.0
age_value = slider["macrodetails/Age"].value / 100
muscle_value = slider["macrodetails-universal/Muscle"].value / 100
```

#### Step 3: Calculate Component Weights

For **stepped components**, interpolate between adjacent values:

```python
# Gender with steps [0.0, 1.0] and values ["female", "male"]
if gender_value <= 0.5:
    weights = {"female": 1.0 - (gender_value / 0.5), "male": gender_value / 0.5}
else:
    weights = {"female": 0.0, "male": 1.0}
```

For **barycentric components**, read the three corner values:

```python
# Phenotype with sum ["African", "Asian", "Caucasian"]
weights = {
    "african": slider["macrodetails/African"].barycentric[0].value,
    "asian": slider["macrodetails/Asian"].barycentric[1].value,
    "caucasian": slider["macrodetails/Caucasian"].barycentric[2].value
}
```

#### Step 4: Generate Target Combinations

Cross-multiply all component weights to generate every combination:

```python
# Example: gender × age × muscle
combinations = []
for gender_name, gender_weight in gender_weights.items():
    for age_name, age_weight in age_weights.items():
        for muscle_name, muscle_weight in muscle_weights.items():
            combined_name = f"{gender_name}-{age_name}-{muscle_name}"
            combined_weight = gender_weight * age_weight * muscle_weight
            if combined_weight > 0.01:  # Skip negligible weights
                combinations.append((combined_name, combined_weight))
```

#### Step 5: Map to Actual Targets

Look up each calculated name in the `targetlink` dictionary:

```python
for calculated_name, weight in combinations:
    if calculated_name in targetlink:
        actual_target = targetlink[calculated_name]
        if actual_target is not None:
            apply_target(actual_target, weight)
```

#### Step 6: Apply Targets

Add all selected targets to the mesh with their calculated weights:

```python
mesh.prepareMacroBuffer()
for target_path, weight in target_applications:
    target_data = load_target(target_path)
    mesh.addTargetToMacroBuffer(weight, target_data)
mesh.addMacroBuffer()  # Apply all at once
```

### Example Calculation

**Scenario:** Slider values are:
- Gender: 0.75 (75% male, 25% female)
- Age: 0.5 (100% young)
- Muscle: 0.25 (50% min, 50% average)

**Component Weights:**
```
gender: {"female": 0.25, "male": 0.75}
age: {"young": 1.0}
muscle: {"minmuscle": 0.5, "averagemuscle": 0.5}
```

**Generated Combinations:**
```
female-young-minmuscle: 0.25 × 1.0 × 0.5 = 0.125
female-young-averagemuscle: 0.25 × 1.0 × 0.5 = 0.125
male-young-minmuscle: 0.75 × 1.0 × 0.5 = 0.375
male-young-averagemuscle: 0.75 × 1.0 × 0.5 = 0.375
```

**Result:** Four targets applied with weights 12.5%, 12.5%, 37.5%, 37.5%

---

## Example Macro Definitions

### Simple 2-Component Macro

**Gender × Age (24 targets)**

```json
{
  "name": "phenotype-gender-age",
  "comp": ["phenotype", "gender", "age"],
  "folder": "macro_age",
  "targets": [
    {"name": "african-female-baby", "t": "african-female-baby"},
    {"name": "african-female-child", "t": "african-female-child"},
    {"name": "african-female-young", "t": "african-female-young"},
    {"name": "african-female-old", "t": "african-female-old"},
    {"name": "african-male-baby", "t": "african-male-baby"},
    {"name": "african-male-child", "t": "african-male-child"},
    {"name": "african-male-young", "t": "african-male-young"},
    {"name": "african-male-old", "t": "african-male-old"},
    ...
  ]
}
```

**Combinations:** 3 phenotypes × 2 genders × 4 ages = 24 targets

### Complex 4-Component Macro

**Gender × Age × Muscle × Weight (72 targets per gender)**

```json
{
  "name": "gender-age-muscle-weight",
  "comp": ["gender", "age", "muscle", "weight"],
  "folder": "macro_muscle",
  "targets": [
    {"name": "female-baby-averagemuscle-averageweight", "t": null},
    {"name": "female-baby-averagemuscle-maxweight", "t": "female-baby-averagemuscle-maxweight"},
    {"name": "female-baby-averagemuscle-minweight", "t": "female-baby-averagemuscle-minweight"},
    {"name": "female-baby-maxmuscle-averageweight", "t": "female-baby-maxmuscle-averageweight"},
    ...
  ]
}
```

**Combinations:** 2 genders × 4 ages × 3 muscles × 3 weights = 72 targets

**Note:** Some targets may be `null` (base state) or reused across combinations.

### Highly Complex 6-Component Macro

**Gender × Age × Muscle × Weight × Cup × Firmness (Female only, 324 targets)**

```json
{
  "name": "gender-age-muscle-weight-cup-firmness",
  "comp": ["gender", "age", "muscle", "weight", "cupsize", "firmness"],
  "folder": "macro_breast",
  "targets": [
    {"name": "female-young-averagemuscle-averageweight-averagecup-averagefirmness", "t": null},
    {"name": "female-young-averagemuscle-averageweight-averagecup-maxfirmness", "t": "young-averagemuscle-averageweight-averagecup-maxfirmness"},
    {"name": "female-young-averagemuscle-averageweight-averagecup-minfirmness", "t": "young-averagemuscle-averageweight-averagecup-minfirmness"},
    ...
  ]
}
```

**Combinations:** 1 gender × 4 ages × 3 muscles × 3 weights × 3 cups × 3 firmnesses = 324 targets (female only)

---

## Target File Naming Strategy

### Systematic Naming

Target files follow the exact naming pattern of component values:

```
{folder}/{value1}-{value2}-{value3}-{...}.target
```

Example:
```
macro_muscle/female-young-maxmuscle-minweight.target
```

### Reuse and Aliasing

Some combinations can share the same target file to reduce duplication:

```json
{"name": "female-baby-minmuscle-averageweight", "t": "universal-baby-minmuscle-averageweight"},
{"name": "male-baby-minmuscle-averageweight", "t": "universal-baby-minmuscle-averageweight"}
```

Both map to the same file because baby muscle/weight isn't gender-specific.

### Abbreviated Naming

Target filenames may use abbreviated forms to reduce redundancy:

```json
{"name": "female-young-averagemuscle-averageweight-averagecup-maxfirmness", 
 "t": "young-averagemuscle-averageweight-averagecup-maxfirmness"}
```

Gender prefix omitted when folder is female-specific.

### Null Targets

Base state combinations may have `null` targets (no morph needed):

```json
{"name": "female-young-averagemuscle-averageweight", "t": null}
```

This represents the default state - no morphing applied.

---

### Connection to base.json

While `macro.json` is independent, it works with targets referenced in `base.json` for measurements and other features.

---

### Example macro.json file

[macro.json](macro.json)

