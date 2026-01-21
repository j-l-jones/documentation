---
title: "makehuman2.conf"
weight: 27
description: "MakeHuman2 configuration file"
---

## makehuman2.conf
# MakeHuman2 Configuration File

## Overview

MakeHuman2 uses JSON-based configuration files to store application settings and preferences. There are two types of configuration files:

- **`makehuman2_default.conf`** - The default system configuration shipped with MakeHuman2
- **`makehuman2.conf`** - The user's personal configuration file

These files control application behavior, UI preferences, data locations, API settings, and keyboard shortcuts.

---

## File Locations

### System Default Configuration

The default configuration is included with the application:

```text
<SystemDir>/data/makehuman2_default.conf
```

This file provides the baseline settings when no user configuration exists.

### User Configuration

The user configuration file is stored in platform-specific locations:

**macOS:**

```text
<~/Library/Application Support/MakeHuman/makehuman2/makehuman2.conf
```

**Linux:**

```text
~/.config/MakeHuman/makehuman2/makehuman2.conf
```

**Windows:**

```text
%APPDATA%\MakeHuman\makehuman2\makehuman2.conf
```

---

## Configuration Loading Process

When MakeHuman2 starts, it loads configuration settings in this priority order:

1. **Check for user configuration** - If `makehuman2.conf` exists in the user config folder, load it
2. **Fall back to default** - If no user config exists, load `makehuman2_default.conf` from the system data folder
3. **Apply defaults** - Any missing keys are filled in with hardcoded default values
4. **Override with environment** - The `MH_HOME_LOCATION` environment variable can override `path_home` (aka ```<UserDataDir>```)
5. **Auto-detect paths** - If `path_home` is not set, the system automatically detects the user's Documents folder (Windows/Linux) or home folder (macOS)

If the configuration is incomplete or this is the first run, MakeHuman2 will automatically create a user configuration file with all necessary settings.

---

## File Format

Both configuration files use JSON format:

```json
{
    "basename": null,
    "noSampleBuffers": false,
    "redirect_messages": false,
    "remember_session": false,
    "theme": "makehuman.qss",
    "units": "metric",
    "apihost": "127.0.0.1",
    "apiport": 12345
}
```

---

## Configuration Options

### `basename`

**Type:** String or null  
**Default:** `null`

Specifies which base mesh to load at startup (e.g., `"hm08"`, `"mh2bot"`).

- If `null`, the application shows the base mesh selection screen
- If set to a valid base name, that mesh loads automatically

**Example:**

```json
"basename": "hm08"
```

---

### `noSampleBuffers`

**Type:** Boolean  
**Default:** `false`

Controls OpenGL multisampling (anti-aliasing).

- `false` - Enable multisampling for smoother rendering (recommended)
- `true` - Disable multisampling (may improve performance on older hardware)

**Example:**

```json
"noSampleBuffers": false
```

---

### `redirect_messages`

**Type:** Boolean  
**Default:** `false` (system default), `true` (user default)

Controls whether application messages and errors are logged to a file.

- `false` - Messages only appear in the console
- `true` - Messages are written to the log file specified in `path_error`

**Example:**

```json
"redirect_messages": true
```

**Related Setting:** Requires `path_error` to be set (automatically added to user config).

---

### `remember_session`

**Type:** Boolean  
**Default:** `false`

Controls whether MakeHuman2 saves and restores the working session.

- `false` - Start fresh each time
- `true` - Restore the last loaded character and settings on startup

**Example:**

```json
"remember_session": true
```

---

### `theme`

**Type:** String  
**Default:** `"makehuman.qss"`

Specifies the Qt stylesheet file used for the application UI.

- Theme files are located in `data/themes/`
- Must be a valid `.qss` file

**Example:**

```json
"theme": "makehuman.qss"
```

**Available Themes:**

- `makehuman.qss` - Default MakeHuman theme

---

### `units`

**Type:** String  
**Default:** `"metric"`

Sets the measurement system used throughout the application.

- `"metric"` - Centimeters, meters, kilograms
- `"imperial"` - Inches, feet, pounds

**Example:**

```json
"units": "imperial"
```

---

### `apihost`

**Type:** String  
**Default:** `"127.0.0.1"`

The IP address the MakeHuman2 API server listens on.

- `"127.0.0.1"` - Only accept connections from localhost (recommended)
- `"0.0.0.0"` - Accept connections from any network interface (security risk)

**Example:**

```json
"apihost": "127.0.0.1"
```

**Usage:** The API server allows external applications (like Blender) to communicate with MakeHuman2.

---

### `apiport`

**Type:** Integer  
**Default:** `12345`

The TCP port number the MakeHuman2 API server listens on.

**Example:**

```json
"apiport": 12345
```

**Note:** If this port is already in use, you may need to change it to another available port.

---

### `path_home` 
*(UserDataDir)*

**Type:** String  
**Not in default config**

Absolute path to the user data folder where MakeHuman2 stores custom content.

**Example:**

```json
"path_home": "/Users/foo/makehuman2"
```

**Contents:**

- Custom targets
- User clothing
- Custom poses
- Exported models
- Character files (.mhm)

**Auto-detection:** If not set, MakeHuman2 automatically detects a default location based on the operating system.

---

### `path_error` 
*(User Config Only)*

**Type:** String  
**Not in default config**

Absolute path to the folder where error logs are stored.

**Example:**

```json
"path_error": "/Users/foo/makehuman2/log"
```

**Usage:** Only used when `redirect_messages` is `true`.

---

### `keys`
*(User Config Only)*

**Type:** Object (key-value pairs)  
**Not included in default config**

Defines custom keyboard shortcuts for camera navigation and other viewport controls. Each key represents an action, and its value is the keyboard shortcut (or array of shortcuts) assigned to that action.

**Format:**
- Single shortcut: String value (e.g., `"Num+9"`)
- Multiple shortcuts: Array of strings (e.g., `["Num+9", "Alt+T"]`)

**Example:**

```json
"keys": {
    "Top": "Num+9",
    "Front": "Num+2",
    "Zoom-In": ["Num++", "Ctrl+="],
    "Pan-Left": "Shift+Left",
    "Toggle Perspective": "Num+0"
}
```

**Available Actions:**

| Action | Default Shortcut | Description |
|--------|------------------|-------------|
| `Top` | `Num+9` | View from top |
| `Bottom` | `Num+7` | View from bottom |
| `Left` | `Num+4` | View from left |
| `Right` | `Num+6` | View from right |
| `Front` | `Num+2` | View from front |
| `Back` | `Num+8` | View from back |
| `Zoom-In` | `Num++` | Zoom camera in |
| `Zoom-Out` | `Num+-` | Zoom camera out |
| `Pan-Left` | `Shift+Left` | Pan camera left |
| `Pan-Right` | `Shift+Right` | Pan camera right |
| `Pan-Up` | `Shift+Up` | Pan camera up |
| `Pan-Down` | `Shift+Down` | Pan camera down |
| `Rotate-Left` | `Ctrl+Left` | Rotate camera left |
| `Rotate-Right` | `Ctrl+Right` | Rotate camera right |
| `Rotate-Up` | `Ctrl+Up` | Rotate camera up |
| `Rotate-Down` | `Ctrl+Down` | Rotate camera down |
| `Stop Animation` | `Esc` | Stop animation playback |
| `Toggle Perspective` | `Num+0` | Toggle perspective/orthographic |

**Key Notation:**

Qt key sequence format is used:
- `Ctrl` - Control key (⌃ on macOS)
- `Shift` - Shift key (⇧)
- `Alt` - Alt key (⌥ Option on macOS)
- `Meta` - Meta key (⊞ Windows key / ⌘ Command on macOS)
- `Num+` - Numeric keypad keys
- `+` - Combines modifiers with keys (e.g., `Ctrl+Z`)
- Regular keys use their character (e.g., `A`, `F1`, `Space`)

**Platform-Specific Modifiers:**

On **macOS**, you can use Command (⌘) and Option (⌥) keys:
- `Meta+Key` - Uses Command key (⌘)
- `Alt+Key` - Uses Option key (⌥)
- `Ctrl+Key` - Uses Control key (⌃)

You can mix and match modifiers based on your preference. For example:

```json
"keys": {
    "Zoom-In": ["Num++", "Ctrl+=", "Meta+="],
    "Zoom-Out": ["Num+-", "Ctrl+-", "Meta+-"],
    "Pan-Left": ["Shift+Left", "Alt+Left"]
}
```

This allows `Ctrl+=`, `Cmd+=` (Meta), and `Num++` to all zoom in.

**Customization:**

You can customize shortcuts in two ways:

1. **Through the Preferences Window:**
   - Open Edit → Preferences → Input tab
   - Select an action in the table
   - Press the desired key combination
   - Click "Change" to apply
   - Click "Save" to write to configuration file

2. **Manual Editing:**
   - Edit `makehuman2.conf` directly
   - Add or modify the `"keys"` object
   - Only include actions you want to customize
   - Restart MakeHuman2 to apply changes

**Example Configuration:**

```json
{
    "basename": "hm08",
    "units": "metric",
    "keys": {
        "Top": ["Num+9", "T"],
        "Front": ["Num+2", "F"],
        "Zoom-In": ["Num++", "=", "Ctrl+="],
        "Zoom-Out": ["Num+-", "-", "Ctrl+-"],
        "Toggle Perspective": ["Num+0", "P"]
    }
}
```

**macOS-Friendly Example:**

For macOS users who prefer Command and Option keys:

```json
{
    "keys": {
        "Top": ["Num+9", "Meta+T"],
        "Front": ["Num+2", "Meta+F"],
        "Zoom-In": ["Num++", "Meta+=", "Ctrl+="],
        "Zoom-Out": ["Num+-", "Meta+-", "Ctrl+-"],
        "Pan-Left": ["Shift+Left", "Alt+Left"],
        "Pan-Right": ["Shift+Right", "Alt+Right"],
        "Rotate-Left": ["Ctrl+Left", "Meta+Left"],
        "Rotate-Right": ["Ctrl+Right", "Meta+Right"],
        "Toggle Perspective": ["Num+0", "Meta+P"]
    }
}
```

This configuration uses:
- `Meta` (⌘ Command) for view changes and zoom
- `Alt` (⌥ Option) for panning
- Both `Ctrl` and `Meta` for rotation, allowing flexibility

**Notes:**

- If the `keys` object is not present, default shortcuts are used
- You can assign multiple shortcuts to the same action using an array
- Duplicate shortcuts across different actions are allowed but may cause conflicts
- Invalid key sequences are ignored and fall back to defaults
- Changes take effect immediately when saved through the Preferences window
- On macOS, `Meta` corresponds to Command (⌘) and `Alt` corresponds to Option (⌥)

---

## Creating a Custom Configuration

### First Time Setup

On the first run, MakeHuman2 automatically:

1. Creates the configuration folder structure
2. Copies `makehuman2_default.conf` settings
3. Adds `path_home` and `path_error` based on system defaults
4. Writes `makehuman2.conf` to the user configuration folder

### Manual Configuration

You can manually create or edit `makehuman2.conf`:

1. Create the configuration folder (see File Locations)
2. Create a JSON file named `makehuman2.conf`
3. Add your preferred settings (only include options you want to change)
4. MakeHuman2 will automatically fill in any missing values with defaults

**Minimal Example:**

```json
{
    "basename": "hm08",
    "units": "imperial",
    "remember_session": true
}
```

---

## Editing Configuration

### Through the Preferences Window

MakeHuman2 provides a preferences window (Edit → Preferences) where you can modify settings:

- Base mesh selection
- Units (metric/imperial)
- User data folder path
- Error log path
- Theme selection
- Session memory toggle
- Message redirection

Changes are automatically saved to `makehuman2.conf` when you click "Save".

### Manual Editing

You can also edit the configuration file directly with a text editor:

1. Close MakeHuman2
2. Open `makehuman2.conf` in a text editor
3. Modify the JSON (ensure valid JSON syntax)
4. Save the file
5. Restart MakeHuman2

**Important:** Ensure the JSON is valid. Use a JSON validator if unsure.

---

## Environment Variable Override

The environment variable `MH_HOME_LOCATION` can override the `path_home` *(UserDataDir)* setting:

**macOS/Linux:**
```bash
export MH_HOME_LOCATION="/path/to/custom/location"
python makehuman.py
```

**Windows:**
```powershell
set MH_HOME_LOCATION=C:\path\to\custom\location
python makehuman.py
```

This is useful for:
- Testing with different data folders
- Running multiple configurations
- Development and debugging

---

## Default Values Reference

These hardcoded defaults are used if a configuration key is missing:

| Key | Default Value |
| --- | ------------- |
| `basename` | `null` |
| `noSampleBuffers` | `false` |
| `redirect_messages` | `true` |
| `remember_session` | `false` |
| `theme` | `"makehuman.qss"` |
| `units` | `"metric"` |
| `apihost` | `"127.0.0.1"` |
| `apiport` | `12345` |

**Note:** The `keys`, `path_home`, and `path_error` settings do not have hardcoded defaults in the system configuration. The `keys` setting uses default shortcuts defined in the code when not present, while `path_home` and `path_error` are auto-detected based on the operating system.

---


