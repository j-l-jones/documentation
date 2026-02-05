---
title: "repository.db"
description: "MakeHuman2 Repository Database"
---

## repository.db
# Repository Database 

## Overview

The `repository.db` file is a SQLite database that serves as a cache for MakeHuman2's asset management system. It stores metadata about all available assets (clothes, hair, eyes, poses, skeletons, etc.) and user customizations, enabling fast asset discovery and filtering without repeatedly scanning the filesystem.

## Location

The database is stored in the user's data directory:
```
<userdata>/dbcache/<basename>/repository.db
```

Where:
- `<userdata>` is the user's MakeHuman2 data directory
- `<basename>` is the currently selected base mesh (e.g., "default", "base1")

## Database Structure

The database consists of two tables:

### Table: `filecache`

Stores metadata about all discovered assets in the MakeHuman2 data directories.

**Columns:**
- `name`: Display name of the asset
- `uuid`: Unique identifier for the asset
- `path`: Absolute file path to the asset
- `folder`: Category/folder type (e.g., "clothes", "hair", "eyes", "models", "rigs", "poses", "expressions")
- `obj_file`: Path to associated 3D object file (if applicable)
- `thumbfile`: Path to thumbnail image file (if available)
- `author`: Creator of the asset
- `tags`: Pipe-separated '|' list of tags for filtering and categorization

**Example data:**
```
name: "Casual Shirt"
uuid: "a1b2c3d4-e5f6-7890-abcd-ef1234567890"
path: "/Users/username/makehuman2/data/clothes/default/casual_shirt.mhclo"
folder: "clothes"
obj_file: "/Users/username/makehuman2/data/clothes/default/casual_shirt.mhbin"
thumbfile: "/Users/username/makehuman2/data/clothes/default/casual_shirt.thumb"
author: "makehuman"
tags: "everyday|casual|top|short-sleeves"
```

### Table: `userinformation`

Stores user-customized tags and metadata that override the default values from `filecache`.

**Columns:**
- `uuid`: Asset UUID (references `filecache.uuid`)
- `tags`: User-defined pipe-separated (|) list of tags

**Purpose:**
- Allows users to customize asset categorization without modifying original asset files
- User tags take precedence over original tags when displaying/filtering assets
- Can be backed up and restored independently

## Cache Management

### Automatic Recreation

The cache is automatically recreated when:
1. The database doesn't exist
2. Asset files are newer than the database timestamp
3. The `-r` or `--repository` command-line flag is used
4. The "Rescan" button is clicked in the asset browser 
   <img src="images/rescan-button.png" alt="Rescan button" width="20%">



### Supported Asset Types

| Folder | File Extension | Description |
|--------|---------------|-------------|
| clothes, eyebrows, eyelashes, eyes, hair, teeth, tongue, proxy | .mhclo | Attachable assets |
| rigs | .mhskel | Skeleton definitions |
| expressions | .mhpose | Facial expressions |
| poses | .bvh | Body poses (BioVision format) |
| models | .mhm | Complete character models |

## User Customization

### Editing Tags

Users can customize asset tags through the Material/Asset Information window:
1. Select an asset in the asset browser
2. Click the information icon
3. Add, modify, or remove tags
4. Save changes

**Note**: User-defined tags are stored in `userinformation` table and do NOT modify the original asset files.

### Tag Format

Tags are stored as pipe-separated strings:
```
everyday|casual|top|short-sleeves
```

When displayed in the UI, they may be shown as separate items or combined for filtering.

## Backup and Restore

### Export User Database

**Menu**: Settings → Backup User Database

   <img src="images/backup-user-db.png" alt="Rescan button" width="40%">

Exports the `userinformation` table to a JSON file:
```json
{
    "uuid-1": "tag1|tag2|tag3",
    "uuid-2": "tag4|tag5",
    ...
}
```

### Import User Database

**Menu**: Settings → Restore User Database

Imports a previously exported JSON file:
- Clears all existing `userinformation` entries
- Loads tags from the JSON file
- Requires program restart to take effect

**Use cases:**
- Backup before major updates
- Share tag customizations between installations
- Restore after moving user data directory

## Command-Line Options

### Force Recreation

```bash
python makehuman.py -r
# or
python makehuman.py --repository
```

Recreates the entire database cache while preserving user-defined tags. Use when:
- Asset discovery seems incomplete
- User data directory was moved
- Corrupted cache suspected
- New assets aren't appearing

## Performance Considerations

### Cache Benefits

- **Fast Startup**: Avoids scanning thousands of files on each launch
- **Quick Filtering**: Database queries are faster than filesystem searches
- **Thumbnail Management**: Centralized thumbnail path tracking
- **User Preferences**: Persistent user customizations

### Cache Limitations

- **Stale Data**: Manual rescan needed after adding assets while program is running
- **Disk Space**: Small overhead (typically < 1MB for standard asset libraries)
- **Sync Issues**: Moving files externally can cause path mismatches

## Troubleshooting

### Assets Not Appearing

1. Use the "Rescan" button in the asset browser
2. Restart MakeHuman with `-r` flag to force recreation
3. Check file permissions on the database
4. Verify asset files are in correct locations

### Lost User Tags

1. Export user database regularly as backup
2. User tags are preserved during cache recreation
3. Only `filecache` table is cleared during rescan

### Database Corruption

If the database becomes corrupted:
1. Close MakeHuman2
2. Delete `repository.db` manually
3. Restart MakeHuman2 (database will be recreated)
4. Import previously exported user tags if available

### Migration Between Systems

When moving MakeHuman2 to a new computer:
1. Export user database on old system
2. Copy user data directory to new system
3. Start MakeHuman2 with `-r` flag to rebuild paths
4. Import user database on new system

