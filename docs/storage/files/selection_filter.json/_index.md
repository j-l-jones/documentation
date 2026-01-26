---
title: "selection_filter.json"
description: "asset categorization and filtering"
---

## selection_filter.json
# Asset Selection Filter System

## Overview

The `selection_filter.json` file defines the **filtering and categorization system** for asset libraries in MakeHuman2. It provides a structured way to organize and search through large collections of assets like clothing, hair, poses, expressions, and rigs by defining hierarchical categories, tag mappings, and quick-access shortcuts.

This system enables users to efficiently browse and filter assets using a tree-based navigation menu, shortcut buttons, and intelligent tag processing that automatically categorizes assets based on their metadata.

## File Location

Each asset type has its own `selection_filter.json` file:

### System Files

```
<SystemDir>/data/<asset-type>/<basename>/selection_filter.json
```

Where:
- `<asset-type>` is: `clothes`, `hair`, `poses`, `expressions`, `rigs`, `eyes`, `models`, `proxy`, etc.
- `<basename>` is the base mesh identifier (e.g., `hm08`, `mh2bot`)

**Examples:**
```
data/clothes/hm08/selection_filter.json
data/hair/hm08/selection_filter.json
data/poses/hm08/selection_filter.json
data/expressions/hm08/selection_filter.json
data/rigs/hm08/selection_filter.json
```

### User Override

```
<UserDataDir>/data/<asset-type>/selection_filter.json
```

User files completely replace system files (not merged).

## Loading Priority

1. **Check user directory:** `<UserDataDir>/data/<asset-type>/selection_filter.json`
2. **Fall back to system:** `<SystemDir>/data/<asset-type>/<basename>/selection_filter.json`
3. **Use empty filters:** If neither exists, no filtering is applied

## File Structure

The file consists of up to four main sections:

```json
{
  "CategoryName": ["value1", "value2", "value3"],
  "NestedCategory": {
    "Subcategory1": ["item1", "item2"],
    "Subcategory2": {
      "DeepCategory": ["deeper1", "deeper2"]
    }
  },
  "Translate": {
    "old-tag": "=new-category:new-value",
    "alias-tag": "category:subcategory",
    "remove-tag": null
  },
  "GuessName": {
    "keyword": "category:subcategory:value"
  },
  "Shortcut": [
    ["icon.png", "filter-expression", "Display Name"]
  ]
}
```

---

## Category Definitions

Categories form the hierarchical tree structure displayed in the filter panel. Each category can be flat (array) or nested (object).

### Flat Categories

Simple one-level categories with a list of values:

```json
"Gender-Age": ["Unisex-Adult", "Male", "Female", "Child", "Baby"]
```

**Creates tree:**
```
Gender-Age
  ├─ Unisex-Adult
  ├─ Male
  ├─ Female
  ├─ Child
  └─ Baby
```

### Nested Categories

Multi-level hierarchies using nested objects:

```json
"Slot": {
  "Head-Neck": {
    "Eyes-Face": ["Glasses", "Mask"],
    "Headgear": ["Hat", "Cap", "Helmet"],
    "Neck": ["Scarf"]
  },
  "Top-Torso": {
    "Layer1": ["Bra", "Tanktop", "Swimwear"],
    "Layer2": ["Shirt", "Sweater", "Pullover"]
  }
}
```

**Creates tree:**
```
Slot
  ├─ Head-Neck
  │   ├─ Eyes-Face
  │   │   ├─ Glasses
  │   │   └─ Mask
  │   ├─ Headgear
  │   │   ├─ Hat
  │   │   ├─ Cap
  │   │   └─ Helmet
  │   └─ Neck
  │       └─ Scarf
  └─ Top-Torso
      ├─ Layer1
      │   ├─ Bra
      │   ├─ Tanktop
      │   └─ Swimwear
      └─ Layer2
          ├─ Shirt
          ├─ Sweater
          └─ Pullover
```

### Tag Path Format

Items in the tree create filter tags with colon-separated paths:

| Tree Path | Generated Tag |
|-----------|---------------|
| `Gender-Age > Male` | `gender-age:male` |
| `Slot > Top-Torso > Layer2 > Shirt` | `slot:top-torso:layer2:shirt` |
| `Era > Contemporary` | `era:contemporary` |

**Note:** All tags are converted to lowercase for matching.

---

## Translate Section

The `Translate` section maps old or informal tags to formal category paths. This helps with legacy assets, common misspellings, or user-friendly aliases.

### Syntax

```json
"Translate": {
  "source-tag": "transformation"
}
```

### Transformation Types

#### 1. Complete Replacement (=)

Replace the tag entirely with a new category path:

```json
"unisex": "=gender-age:unisex-adult",
"vintage": "=era:past",
"hats": "=slot:head-neck:headgear:hat"
```

**Effect:**
- Asset tagged `"unisex"` becomes `"gender-age:unisex-adult"`
- Asset tagged `"vintage"` becomes `"era:past"`
- The original tag is completely replaced

#### 2. Prepend Category

Add category prefix to existing tag:

```json
"short": "style"
```

**Effect:**
- Asset tagged `"short"` becomes `"style:short"`
- The tag value is preserved, category is added

#### 3. Remove Tag (null)

Remove unwanted tags entirely:

```json
"unisex": null,
"hair": null
```

**Effect:**
- Tags `"unisex"` and `"hair"` are removed from assets
- Useful for cleaning up redundant or meaningless tags

### Examples

**Clothing Example:**
```json
"Translate": {
  "unisex": "=gender-age:unisex-adult",
  "vintage": "=era:past",
  "hats": "=slot:head-neck:headgear:hat",
  "lingerie": "=slot:top-torso:layer1:bra"
}
```

**Hair Example:**
```json
"Translate": {
  "unisex": null,
  "hair": null,
  "short hair": "=style:short",
  "ladies": "=gender-age:female",
  "mens": "=gender-age:male"
}
```

**Poses Example:**
```json
"Translate": {
  "Game": "=situation:reference-pose",
  "Action": "=activity:fighting"
}
```

---

## GuessName Section

The `GuessName` section automatically assigns tags to assets based on keywords found in the asset's filename or name.

### Syntax

```json
"GuessName": {
  "keyword-in-name": "category:subcategory:value"
}
```

### How It Works

When an asset is loaded, the system:
1. Checks if any `GuessName` keyword appears in the asset name (case-insensitive)
2. If found, automatically adds the specified tag to the asset
3. This happens in addition to any tags already defined in the asset's metadata

### Examples

**Clothing Example:**
```json
"GuessName": {
  "suit": "slot:top-torso:layer3:suit"
}
```

**Effect:** Any asset with "suit" in its name (e.g., `"business-suit.mhclo"`, `"suit-jacket.mhclo"`) automatically gets tagged as `"slot:top-torso:layer3:suit"`.

**Poses Example:**
```json
"GuessName": {
  "fight": "activity:fighting"
}
```

**Effect:** Poses named `"fight-stance.mhpose"`, `"fighting-pose.mhpose"`, etc., automatically get `"activity:fighting"` tag.

### Use Cases

- **Lazy tagging:** Automatically categorize assets without manual tagging
- **Legacy support:** Add proper categories to old assets that lack metadata
- **Bulk organization:** Apply consistent tags across large asset collections
- **Naming conventions:** Leverage existing naming patterns

---

## Shortcut Section

The `Shortcut` section creates quick-access icon buttons that apply complex filter combinations with a single click. These appear as a toolbar above the asset browser.

### Syntax

```json
"Shortcut": [
  ["icon-file.png", "filter-expression", "Tooltip Text"]
]
```

### Array Structure

Each shortcut is a 3-element array:

1. **Icon filename** (string): PNG image file for the button
   - Located in `data/icons/` (system) or `~/makehuman/v1py3/data/icons/` (user)
   - Prefix with `"u:"` to force user directory: `"u:custom-icon.png"`

2. **Filter expression** (string): Complex filter logic using special syntax

3. **Tooltip** (string): Descriptive text shown on hover

### Filter Expression Syntax

Filter expressions use boolean logic to combine multiple criteria:

#### Basic Filter

```
"category:subcategory:value"
```

**Example:**
```
"gender-age:female"
```
Shows only female assets.

#### AND Operator (&)

Combine multiple required criteria:

```
"criteria1&criteria2&criteria3"
```

**Example:**
```
"gender-age:female&slot:top-torso:layer1:bra"
```
Shows female assets that are also bras.

#### OR Operator (|)

Allow any of several criteria:

```
"criteria1|criteria2|criteria3"
```

**Example:**
```
"slot:top-torso:layer1:bra|slot:bottom:layer1:panties"
```
Shows assets that are either bras or panties.

#### Combining AND and OR

Complex logic using both operators:

```
"required1&required2&(option1|option2)"
```

**Example:**
```
"gender-age:female&slot:top-torso:layer1:bra|slot:bottom:layer1:panties"
```
Shows female assets that are either bras or panties.

### Common Shortcuts

**Simple Category Shortcuts:**
```json
["glasses.png", "slot:head-neck:eyes-face:glasses", "Glasses"],
["hat.png", "slot:head-neck:headgear:hat", "Hat"],
["shirt.png", "slot:top-torso:layer2", "Shirt, Tops"]
```

**Gender-Specific Shortcuts:**
```json
["f_shoe.png", "slot:feet&gender-age:female|slot:feet&gender-age:unisex-adult", "Shoes (female)"],
["m_shoe.png", "slot:feet&gender-age:male|slot:feet&gender-age:unisex-adult", "Shoes (male)"]
```

**Complex Multi-Category Shortcuts:**
```json
["lingerie.png", "gender-age:female&slot:top-torso:layer1:bra|slot:bottom:layer1:panties", "Lingerie"],
["maleunderwear.png", "gender-age:male&slot:top-torso:layer1|slot:bottom:layer1:panties", "Male Underwear"]
```

---

## How Tag Processing Works

### Step 1: Asset Loading

When an asset is loaded, it has tags defined in its metadata (e.g., in `.mhclo`, `.mhpose` files):

```
tags = ["female", "casual", "shirt"]
```

### Step 2: GuessName Processing

Check if asset name contains keywords:

```python
# Asset name: "summer-suit.mhclo"
# GuessName: {"suit": "slot:top-torso:layer3:suit"}
# Result: Add "slot:top-torso:layer3:suit" to tags
```

### Step 3: Translate Processing

Apply tag transformations:

```python
# Original tags: ["female", "casual", "vintage"]
# Translate: {"vintage": "=era:past"}
# Result: ["female", "casual", "era:past"]
```

### Step 4: Category Prepending

For tags matching category values, prepend the category path:

```python
# Original: ["female", "casual"]
# Categories: {"Gender-Age": ["Female"], "Occasion": ["Casual"]}
# Result: ["gender-age:female", "occasion:casual"]
```

### Step 5: Final Tag Set

Asset now has fully qualified tags for filtering:

```
Final tags: ["gender-age:female", "occasion:casual", "era:past", "slot:top-torso:layer3:suit"]
```
---


### Sample selection_filter.json file

[data/clothes/hm08/selection_filter.json](selection_filter.json)

