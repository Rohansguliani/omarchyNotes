# Major Omarchy Configuration Changes Summary

## Overview
This document summarizes all major configuration changes made to the Omarchy setup, beyond the two main ones (4-finger swipe and Alt+1 screenshot).

## Already Documented

1. ✅ **4-Finger Swipe Gesture** - See `4-finger-swipe-gesture.md`
2. ✅ **Alt+1 Screenshot** - See `alt-1-screenshot.md`

## Other Major Changes

### 1. Window Gaps Set to Zero (No Gaps)

**File**: `~/.config/hypr/looknfeel.conf`

**Configuration**:
```
general {
    gaps_in = 0
    gaps_out = 0
}
```

**What it does**:
- Removes all spacing between windows
- Removes spacing between windows and waybar
- Creates a tiled, no-gap appearance

**Quick Enable**:
```bash
hyprctl keyword general:gaps_in 0
hyprctl keyword general:gaps_out 0
hyprctl reload
```

---

### 2. Screensavers Disabled (No Auto-Lock)

**Files**:
- `~/.config/hypr/hypridle.conf` (commented out)
- `~/.config/hypr/autostart.conf` (hypridle disabled)

**What it does**:
- Prevents automatic screen lock
- Prevents screensaver activation
- Prevents display timeout
- Keeps screen always on

**Note**: Currently `autostart.conf` shows `exec-once = uwsm-app -- hypridle`, so this may have been re-enabled or changed.

**To Disable**:
```bash
pkill hypridle
# Then comment out hypridle in autostart.conf
```

---

### 3. X11 Chromium Mode (For Google Sign-In)

**File**: `~/.local/share/omarchy/bin/omarchy-launch-browser`

**What it does**:
- Launches Chromium in X11 mode instead of Wayland
- Enables Google OAuth authentication (which Wayland blocks)
- Allows Google account sign-in

**Command**:
```bash
chromium --ozone-platform=x11
```

**Note**: This is likely configured in the browser launch script.

---

## Quick Reference: All Changes

### Input/Touchpad
- ✅ 4-finger swipe gesture (documented separately)

### Appearance
- ✅ Window gaps: 0 (no gaps)

### Applications
- ✅ Alt+1 screenshot (documented separately)
- ✅ X11 Chromium mode

### System
- ⚠️ Screensavers disabled (may be re-enabled)

## Files Modified

1. `~/.config/hypr/input.conf` - Gestures
2. `~/.config/hypr/bindings.conf` - Alt+1 screenshot
3. `~/.config/hypr/looknfeel.conf` - Gaps
4. `~/.config/hypr/autostart.conf` - Startup apps

## References

- Original documentation: `~/changesOmarchy.txt`
- Individual change docs in `~/omarchyChanges/` folder

