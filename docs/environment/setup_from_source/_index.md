---
title: "Setup from Source"
weight: 10
bookCollapseSection: false
---

## Overview

This guide walks you through setting up and running MakeHuman 2 from source code for the first time. Currently, there is no installer available, so running from source is the primary method of using the application.

## Quick Start

For experienced developers who want to get started immediately:

1. Create the configuration folder (see platform-specific paths below)
2. Set up a Python virtual environment: `virtualenv venv`
3. Activate the environment: `source venv/bin/activate` (macOS/Linux)
4. Install dependencies: `pip install -r requirements.txt`
5. Run the application: `python makehuman.py`
6. Select a base mesh on first launch to enable all features

Continue reading for detailed explanations of each step and configuration options.

## Prerequisites

Before you begin, ensure you have the following installed:

- **Python 3.x** (Python 3.13+ recommended)
- **pip** (Python package manager)
- **virtualenv** (for creating isolated Python environments)
- **Git** (to clone the source repository)

## Initial Setup

Before running the app for the first time, you need to create the configuration folder and optionally set up a custom configuration file


### Configuration Folder

The configuration folder stores the application's configuration file and must be created before first run.

#### Creating the Configuration Folder

**macOS:**

```bash
mkdir -p ~/Library/Application\ Support/MakeHuman/makehuman2
```

**Linux:**

```bash
mkdir -p ~/.config/MakeHuman/makehuman2
```

**Windows:**

```powershell
mkdir %APPDATA%\MakeHuman\makehuman2
```

### Configuration File

The configuration file (`makehuman2.conf`) lives in the configuration folder and provides basic application settings.

#### Configuration File Location

**macOS:**

```text
~/Library/Application Support/MakeHuman/makehuman2/makehuman2.conf
```

**Linux:**

```text
~/.config/MakeHuman/makehuman2/makehuman2.conf
```

**Windows:**

```text
%APPDATA%\MakeHuman\makehuman2\makehuman2.conf
```

#### Configuration File Format

The configuration file is a JSON file with the following structure:

```json
{
    "apihost": "127.0.0.1",
    "apiport": 12345,
    "basename": null,
    "noSampleBuffers": false,
    "path_error": "/Users/foo/makehuman2/log",
    "path_home": "/Users/foo/makehuman2",
    "redirect_messages": false,
    "remember_session": false,
    "theme": "makehuman.qss",
    "units": "metric"
}
```

**Key Configuration Options:**

- `path_home`: User data folder location (see User Data Folder section)
- `path_error`: Location for error logs
- `apihost` / `apiport`: API server settings
- `theme`: UI theme file
- `units`: Measurement system (metric or imperial)
- `redirect_messages`: Enable logging to file

#### Default Configuration

If you start the application without a configuration file, a minimal default configuration is automatically created in your default User Data folder based on your os. It will create a folder there called 'makehuman2' if it doesn't exist.

## Directory Structure

Understanding the directory structure is important for properly configuring the application.

### System Folder

The **System Folder** (also called `path_sys`) is the location from where the app is launched—typically your local source code repository root.

**Example:**

```text
/Users/foo/src/makehuman2
```

### System Data Folder

The **System Data Folder** (also called `path_sysdata`) holds system-level resources and is located under the System Folder.

**Example:**

```text
/Users/foo/src/makehuman2/data
```

This folder contains the default assets and resources that ship with the application.

### User Data Folder

The **User Data Folder** (also called `path_home`) holds user-specific resources, customizations, and content.

When the application needs a file, it first checks the User Data Folder. If the file isn't found there, it falls back to the System Data Folder.


#### Configuring the User Data Folder

When running from source, it's recommended to set a custom User Data Folder in your configuration file:

**macOS Example:**

```json
"path_home": "/Users/foo/makehuman2_data"
```

#### Using an Environment Variable

You can also configure the User Data Folder using the `MH_HOME_LOCATION` environment variable. When set, this environment variable takes precedence over the configuration file setting.

**Example:**

```bash
export MH_HOME_LOCATION="/Users/foo/makehuman2_data"
```

**Note:** Internally, the application uses `path_userdata`, which points to `path_home/data`.


#### First-Time Initialization

The first time the application runs, it automatically creates the following subfolders in the User Data Folder:

- `clothes` - Custom clothing items
- `dbcache` - Database cache files
- `downloads` - Downloaded content
- `eyebrows` - Custom eyebrow assets
- `eyelashes` - Custom eyelash assets
- `eyes` - Custom eye assets
- `exports` - Exported models and files
- `expressions` - Custom facial expressions
- `grab` - Grab tool presets
- `hair` - Custom hair assets
- `models` - Custom models
- `poses` - Custom poses and animations
- `proxy` - Proxy mesh files
- `rigs` - Custom rig configurations
- `shaders` - Custom shader files
- `skins` - Custom skin textures
- `target` - Custom morph targets
- `teeth` - Custom teeth assets
- `themes` - Custom UI themes
- `tongue` - Custom tongue assets


### Logging

Application logs are written to the location specified in the `path_error` configuration setting.

**Default Log Location:**

```text
<path_home>/log
```

To enable file logging, set `redirect_messages` to `true` in your configuration file. When enabled, application messages and error logs will be written to this directory instead of only appearing in the console.

## Installation Steps

### 1. Set Up Python Virtual Environment

Create an isolated Python environment for the project:

```bash
virtualenv venv
```

**Expected Output:**

```text
created virtual environment CPython3.13.9.final.0-64 in 185ms
  creator CPython3Posix(dest=/path/to/makehuman2/venv, clear=False, no_vcs_ignore=False, global=False)
  seeder FromAppData(download=False, pip=bundle, via=copy, app_data_dir=~/.local/share/virtualenv)
    added seed packages: pip==25.3
  activators BashActivator,CShellActivator,FishActivator,NushellActivator,PowerShellActivator,PythonActivator
```

### 2. Activate the Virtual Environment

**macOS/Linux:**

```bash
source venv/bin/activate
```

**Windows:**

```powershell
venv\Scripts\activate
```

After activation, your command prompt should be prefixed with `(venv)`.

### 3. Install Dependencies

Install all required Python packages:

```bash
pip install -r requirements.txt
```

This will install all dependencies specified in the `requirements.txt` file, including:

- Qt libraries for the UI
- NumPy for numerical operations
- Image processing libraries
- And other required dependencies

## Running the Application

### Launch MakeHuman 2

From your source code directory (with the virtual environment activated):

```bash
python makehuman.py
```

### First Launch

When you run the application for the first time, you'll see the main interface:

![initial launch](initial_launch_first_time.png)

**Important:** On first launch, you must select a base mesh from the available options to enable all application features. The base mesh serves as the foundation for character creation and customization.


## Troubleshooting

### Common Issues

#### `virtualenv: command not found`

Install virtualenv:

```bash
pip install virtualenv
```

#### Python Version Errors

Ensure you have Python 3.x installed:

```bash
python --version
```

If you have multiple Python versions, you may need to use `python3` instead of `python`.

#### Missing Dependencies

Make sure you've activated the virtual environment before running pip install:

```bash
source venv/bin/activate  # macOS/Linux
pip install -r requirements.txt
```

#### Configuration Not Found

If the application can't find your configuration, verify:

1. The configuration folder exists (see Configuration Folder section)
2. The `makehuman2.conf` file is in the correct location
3. The JSON in the configuration file is valid

#### Assets Not Loading

Ensure:

1. Your `path_home` is correctly set in the configuration file
2. The User Data Folder has been initialized (check for subfolders)
3. The System Data Folder exists in your source directory


## Next Steps

After successfully running the application:

1. **Explore the Interface:** Familiarize yourself with the UI and available tools
2. **Customize Settings:** Adjust the configuration file to suit your workflow
3. **Add Content:** Place custom assets in the appropriate User Data Folder subfolders
4. **Development:** If you're contributing code, refer to the development documentation

## Additional Resources

- Check the other documentation sections for detailed information on specific features
- Join the community forums for support and discussion
- Report bugs or suggest features on the project's issue tracker

---

**Note:** This documentation is based on running MakeHuman 2 from source. Once an installer becomes available, the setup process will be simplified.













