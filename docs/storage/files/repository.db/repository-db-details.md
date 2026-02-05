
## API Reference

The database is accessed through the `FileCache` class in `core/sql_cache.py`:

### Key Methods

```python
# Initialize cache
fileCache = FileCache(env, dbname)

# Create/update cache
fileCache.createCache(latest_timestamp, subdir="clothes", force=False)

# Query cache
rows, user_overrides = fileCache.listCacheMatch()

# User tag management
fileCache.insertParamUser(uuid, tags)
fileCache.getEditParamUser(uuid)
fileCache.deleteParamUser(uuid)

# Export/Import
fileCache.exportUserInfo(filename)
fileCache.importUserInfo(filename)

# Update thumbnails
fileCache.updateParamInfo(uuid, thumbfile)
```

### Cache Update Process

1. **Timestamp Check**: Compare database modification time with newest asset file
2. **Selective Update**: 
   - If a specific folder is rescanned, only that folder's entries are deleted and rebuilt
   - If forced recreation, entire `filecache` table is cleared
3. **Filesystem Scan**: Recursively scan data directories for supported file types:
   - `.mhclo` - Clothes/assets
   - `.mhskel` - Skeleton rigs
   - `.mhpose` - Expressions
   - `.bvh` - Motion capture poses
   - `.mhm` - Character models
4. **Metadata Extraction**: Parse each file to extract name, UUID, author, and tags
5. **Thumbnail Detection**: Check for corresponding `.thumb` files
6. **Database Insert**: Bulk insert all discovered assets

## Implementation Details

### Database Schema Creation

```sql
CREATE TABLE filecache(
    name, 
    uuid, 
    path, 
    folder, 
    obj_file, 
    thumbfile, 
    author, 
    tags
)

CREATE TABLE userinformation(
    uuid, 
    tags
)
```

### Tag Merging Logic

When displaying assets:
1. Query both `filecache` and `userinformation` tables
2. Match entries by UUID
3. If user tags exist for a UUID, they replace the original tags
4. Original tags in asset files remain unchanged

## Related Files

- `core/sql_cache.py` - FileCache class implementation
- `core/globenv.py` - Filesystem scanning and cache initialization
- `gui/materialwindow.py` - Asset information editor UI
- `gui/mainwindow.py` - Backup/restore functionality

