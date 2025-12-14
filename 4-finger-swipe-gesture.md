# 4-Finger Swipe Gesture Configuration

## Overview
This document tracks the configuration for 4-finger horizontal swipe gestures to switch between workspaces/desktops in Hyprland on Omarchy.

## System Information
- **Desktop Environment**: Omarchy (Hyprland-based)
- **Hyprland Version**: 0.51.1
- **Package Dependencies**: 
  - `libinput` (version 1.29.1-1) - handles all input device processing including touchpad gestures
  - `xf86-input-libinput` (version 1.5.0-1) - X11 libinput driver (backup support)

## How It Was Working Before (Initial Setup)

### Original Working Configuration

**File**: `~/.config/hypr/input.conf`

**Configuration Line**:
```
gesture = 4, horizontal, workspace
```

**What it did**:
- Enabled 4-finger horizontal swipe gestures
- Swipe **left** → moved to next workspace
- Swipe **right** → moved to previous workspace
- **LIMITATION**: Only navigated to workspaces that had windows on them
- Would skip empty workspaces and stop at the last workspace with windows

### How It Worked Technically

1. **libinput** detects multi-finger touchpad input at the kernel/driver level
2. **Hyprland** receives the gesture events through Wayland protocols
3. The `gesture = 4, horizontal, workspace` config tells Hyprland to interpret 4-finger horizontal swipes as workspace-switching commands
4. Hyprland's default behavior only navigates to **existing/occupied** workspaces

### Quick Enable Command (Original Method)

You could enable this on-the-fly without editing the config file using:
```bash
hyprctl keyword gesture 4 horizontal workspace
```

Then reload:
```bash
hyprctl reload
```

## Current Problem

### Issue Description
After attempting to enable swiping to empty workspaces, the gesture stopped working entirely.

### What Was Attempted

**Modified Configuration** (in `~/.config/hypr/input.conf`):
```
gestures {
    workspace_swipe_forever = true
    workspace_swipe_create_new = false
    workspace_swipe_use_r = true
}

gesture = 4, horizontal, workspace

workspace = 1, persistent:true
workspace = 2, persistent:true
workspace = 3, persistent:true
workspace = 4, persistent:true
workspace = 5, persistent:true
```

### Problems Encountered

1. **Hyprland Version Compatibility**: Hyprland 0.51 has a completely reworked gesture system
   - Old options like `workspace_swipe` and `workspace_swipe_fingers` were **removed**
   - New system uses `gesture = fingers, direction, action` syntax
   - Some modifier options still exist (`workspace_swipe_forever`, `workspace_swipe_distance`, etc.)

2. **Conflicting Configuration**: The combination of the new `gestures {}` block with the old `gesture =` line may have caused conflicts

3. **Persistent Workspaces**: The `workspace = N, persistent:true` syntax may not be working as expected

4. **Current Status**: 4-finger swipe gesture is not responding at all

## Known Working Options (Hyprland 0.51.1)

These options exist and can be checked:
```bash
hyprctl getoption gestures:workspace_swipe_forever
# Returns: int: 1, set: true (when enabled)

hyprctl getoption gestures:workspace_swipe_distance
# Returns: int: 300 (default)

hyprctl getoption gestures:workspace_swipe_create_new
# Returns: int: 0 (default: false)

hyprctl getoption gestures:workspace_swipe_use_r
# Returns: int: 1 (default: true)
```

## Files Involved

1. **Primary Config**: `~/.config/hypr/input.conf`
2. **Omarchy Default Config**: `~/.local/share/omarchy/config/hypr/input.conf`
3. **Main Config**: `~/.config/hypr/hyprland.conf` (sources input.conf)

## Next Steps to Fix

1. **Revert to original working configuration**:
   ```
   gesture = 4, horizontal, workspace
   ```

2. **Test if basic gesture works again**

3. **Research Hyprland 0.51 gesture documentation** for correct syntax to enable swiping to empty workspaces

4. **Check if persistent workspaces are needed** or if `workspace_swipe_forever` alone should work

5. **Verify libinput is detecting 4-finger gestures**:
   ```bash
   libinput debug-events
   ```

## References

- Hyprland Gesture Documentation: https://wiki.hyprland.org/Configuring/Gestures/
- Hyprland 0.51 Update Notes: https://hypr.land/news/update51/
- Original working config documented in: `~/changesOmarchy.txt` (line 12-18)

