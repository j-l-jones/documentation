# Configuration File (makehuman2.conf / makehuman2_default.conf)

## Technical Implementation

### Loading

The configuration loading is implemented in `core/globenv.py` in the `programInfo.environment()` method:

1. Defines default configuration dictionary
2. Reads system paths
3. Loads `makehuman2_version.json`
4. Attempts to load user config from `path_userconf`
5. Falls back to `makehuman2_default.conf` if user config doesn't exist
6. Fills in any missing keys with defaults using `dictFillGaps()`
7. Checks for `MH_HOME_LOCATION` environment variable
8. Auto-detects `path_home` if not set (platform-specific)
9. Writes updated config if changes were made

### Saving

Configuration is saved via the preferences window in `gui/prefwindow.py`:

- User modifies settings in the preferences UI
- On "Save" click, the `save_call()` method updates `env.config`
- Calls `env.writeJSON(env.path_userconf, env.config)`
- Writes the complete configuration to the user config file

---

## Troubleshooting

### Configuration Not Found

**Symptom:** Application reports configuration file not found

**Solution:**
1. Ensure the configuration folder exists (see File Locations)
2. MakeHuman2 will automatically create a new config on first run
3. Verify you have write permissions to the configuration folder
