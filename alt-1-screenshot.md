# Alt+1 Screenshot Configuration

## Overview
This document tracks the configuration for Alt+1 keyboard shortcut to take screenshots using hyprshot in region selection mode.

## How It Works

### Keyboard Binding

**File**: `~/.config/hypr/bindings.conf`

**Configuration Line** (line 29):
```
bind = ALT, 1, exec, hyprshot -m region
```

**What it does**:
- Press **Alt+1** to trigger screenshot region selection
- Opens hyprshot in region mode (`-m region`)
- Allows you to click and drag to select the area you want to screenshot
- Automatically saves the screenshot to `~/Pictures/` and copies it to clipboard
- **Note**: To only copy to clipboard without saving, see [Clipboard-Only Mode](#clipboard-only-mode-no-file-saving) section below

### Quick Enable Command

You can enable this binding on-the-fly without editing the config file:
```bash
hyprctl keyword bind ALT,1,exec,hyprshot -m region
```

Then reload Hyprland:
```bash
hyprctl reload
```

## Tool Details

### hyprshot

**Package**: `hyprshot` (version 1.3.0-4)
**Location**: `/usr/bin/hyprshot`
**Purpose**: Utility to easily take screenshots in Hyprland using your mouse

**Available Modes**:
- `output` - take screenshot of an entire monitor
- `window` - take screenshot of an open window
- `region` - take screenshot of selected region (used by Alt+1)
- `active` - take screenshot of active window/output

**Key Options**:
- `-m, --mode` - specify capture mode (one of: output, window, region, active, OUTPUT_NAME)
- `-o, --output-folder` - directory in which to save screenshot
- `-f, --filename` - the file name of the resulting screenshot
- `--clipboard-only` - copy screenshot to clipboard and don't save image to disk
- `-z, --freeze` - freeze the screen on initialization
- `-s, --silent` - don't send notification when screenshot is saved

### Region Mode Behavior

When using `hyprshot -m region`:
1. Screen freezes/overlays to show selection area
2. Click and drag to select the region you want
3. Screenshot is automatically taken of the selected region
4. Saved to default screenshot directory (usually `~/Pictures/`)
5. Copied to clipboard automatically

## Additional Configuration

### hyprshot Window Rules

**File**: `~/.local/share/omarchy/default/hypr/apps/hyprshot.conf`

**Configuration**:
```
# Remove 1px border around hyprshot screenshots
layerrule = noanim, selection
```

This removes animation and 1px border around the selection overlay for a cleaner experience.

## Screenshot Storage

**Default Location**: `~/Pictures/`
**Filename Format**: `screenshot-YYYY-MM-DD_HH-MM-SS.png`
**Example**: `screenshot-2025-09-27_04-20-52.png`

The screenshot directory can be customized via:
- `OMARCHY_SCREENSHOT_DIR` environment variable
- `XDG_PICTURES_DIR` environment variable (from `~/.config/user-dirs.dirs`)
- Falls back to `$HOME/Pictures` if neither is set

## Clipboard-Only Mode (No File Saving)

To prevent screenshots from being saved to disk and only copy them to clipboard, use the `--clipboard-only` flag.

### Modified Binding

**File**: `~/.config/hypr/bindings.conf`

**Change from**:
```
bind = ALT, 1, exec, hyprshot -m region
```

**To**:
```
bind = ALT, 1, exec, hyprshot -m region --clipboard-only
```

### Quick Enable Command

You can enable clipboard-only mode on-the-fly:
```bash
hyprctl keyword bind ALT,1,exec,hyprshot -m region --clipboard-only
```

Then reload Hyprland:
```bash
hyprctl reload
```

### Behavior with `--clipboard-only`

When using `--clipboard-only`:
- Screenshot is **only** copied to clipboard
- **No file** is saved to disk
- No notification about file saving (though hyprshot may still show a clipboard notification)
- Useful for quick screenshots you want to paste directly without cluttering your Pictures folder

## Alternative Screenshot Methods

### Omarchy Built-in Screenshot Command

**Script**: `~/.local/share/omarchy/bin/omarchy-cmd-screenshot`

This is a more advanced screenshot tool that:
- Uses `grim` for capture and `satty` for editing
- Supports multiple modes: `region`, `windows`, `fullscreen`, `smart`
- Can copy directly to clipboard without saving
- Opens screenshot in satty editor for annotation before saving

**Default Bindings** (from Omarchy defaults):
- `PRINT` key: `omarchy-cmd-screenshot`
- `SHIFT+PRINT`: `omarchy-cmd-screenshot smart clipboard`

**Usage Examples**:
```bash
omarchy-cmd-screenshot              # Smart mode with editing
omarchy-cmd-screenshot region         # Region selection
omarchy-cmd-screenshot windows        # Window selection
omarchy-cmd-screenshot fullscreen     # Full screen
omarchy-cmd-screenshot smart clipboard # Smart mode, copy to clipboard only
```

## Files Involved

1. **Primary Binding**: `~/.config/hypr/bindings.conf` (line 29)
2. **Window Rules**: `~/.local/share/omarchy/default/hypr/apps/hyprshot.conf`
3. **Alternative Tool**: `~/.local/share/omarchy/bin/omarchy-cmd-screenshot`
4. **Main Config**: `~/.config/hypr/hyprland.conf` (sources bindings.conf)

## Dependencies

- **hyprshot** - Screenshot utility (installed via pacman)
- **slurp** - Region selection tool (used by omarchy-cmd-screenshot)
- **grim** - Screenshot tool for Wayland (used by omarchy-cmd-screenshot)
- **satty** - Screenshot annotation/editing tool (used by omarchy-cmd-screenshot)
- **wl-copy** - Wayland clipboard utility (used for copying screenshots)

## Testing

### Default Mode (Saves to Disk)
To test the Alt+1 screenshot binding:
1. Press **Alt+1**
2. Click and drag to select a region
3. Screenshot should be saved to `~/Pictures/` and copied to clipboard
4. Check `~/Pictures/` for the screenshot file

### Clipboard-Only Mode
To test clipboard-only mode:
1. Ensure binding uses `--clipboard-only` flag
2. Press **Alt+1**
3. Click and drag to select a region
4. Screenshot should be copied to clipboard only
5. Verify no file was created in `~/Pictures/`
6. Paste (Ctrl+V) to verify clipboard contains the screenshot

## Notes

- The Alt+1 binding uses the simpler `hyprshot` tool directly
- For more advanced features (editing, annotation), use `omarchy-cmd-screenshot` instead
- Screenshots are automatically copied to clipboard
- The region selection overlay has no animation/border for cleaner UX

## References

- Original config documented in: `~/changesOmarchy.txt` (line 71-77)
- hyprshot help: `hyprshot --help`
- Omarchy screenshot script: `~/.local/share/omarchy/bin/omarchy-cmd-screenshot`

