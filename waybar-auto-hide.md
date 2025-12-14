# Waybar Auto-Hide Configuration

## Overview
This document explains how to configure waybar to automatically hide when the cursor is away from the top of the screen, and show when hovering near the top. When hidden, windows extend all the way to the top edge with no gap.

## How It Works

### Script: `~/.local/bin/waybar-auto-hide`

The script monitors cursor position and:
1. **Hides waybar** when cursor is more than 60px from top
   - Kills the waybar process completely
   - Windows extend to top (no gap changes needed)
2. **Shows waybar** when cursor is within 40px of top
   - Starts waybar process
   - Waybar overlays on top of windows (no window movement)

### Configuration

**Thresholds** (adjustable in script):
- `SHOW_THRESHOLD=40` - Show waybar when cursor is within 40px of top
- `HIDE_THRESHOLD=60` - Hide waybar when cursor is more than 60px from top
- `CHECK_INTERVAL=0.1` - Check cursor position every 100ms

**Key Design Decision:**
- **No gap changes** - Waybar overlays on windows instead of pushing them down
- This prevents horizontal window movement when waybar shows/hides
- Windows stay in place, only waybar appears/disappears

### Behavior

**When waybar is hidden:**
- Waybar process is killed (completely gone, not just transparent)
- Windows extend all the way to the top edge
- No gap above windows
- Full screen real estate for applications

**When waybar is shown:**
- Waybar process starts
- Waybar appears fully opaque (no transparency)
- Waybar overlays on top of windows
- **No window movement** - windows stay in place (no horizontal or vertical resizing)

## Installation

### 1. Create the Script

The script is located at: `~/.local/bin/waybar-auto-hide`

**Script contents:**
```bash
#!/bin/bash

# Waybar auto-hide script - Simplified version
HIDE_THRESHOLD=60
SHOW_THRESHOLD=40
CHECK_INTERVAL=0.1

show_waybar() {
    if ! pgrep -x waybar > /dev/null; then
        uwsm-app -- waybar &
        sleep 0.3
    fi
}

hide_waybar() {
    pkill -x waybar 2>/dev/null
    sleep 0.1
}

STATE="hidden"
hide_waybar

# Set gaps once at startup - waybar will overlay, windows stay put
hyprctl keyword general:gaps_out "0 0 0 0" 2>/dev/null

while true; do
    Y=$(hyprctl cursorpos -j 2>/dev/null | jq -r '.y // 999')
    
    if [ "$Y" -le "$SHOW_THRESHOLD" ] && [ "$STATE" != "visible" ]; then
        show_waybar
        STATE="visible"
    elif [ "$Y" -gt "$HIDE_THRESHOLD" ] && [ "$STATE" != "hidden" ]; then
        hide_waybar
        STATE="hidden"
    fi
    
    sleep "$CHECK_INTERVAL"
done
```

### 2. Make Script Executable

```bash
chmod +x ~/.local/bin/waybar-auto-hide
```

### 3. Add to Autostart

**File**: `~/.config/hypr/autostart.conf`

Add this line:
```
exec-once = ~/.local/bin/waybar-auto-hide
```

### 4. Restart Hyprland or Reload Config

```bash
hyprctl reload
```

Or restart Hyprland completely.

## How It Works Technically

1. **Cursor Position Monitoring**: Uses `hyprctl cursorpos -j` to get cursor Y coordinate
2. **State Management**: Tracks whether waybar is "visible" or "hidden"
3. **Process Management**: Uses `pkill -x waybar` to kill waybar, `uwsm-app -- waybar &` to start it
   - **Important**: Omarchy requires `uwsm-app` wrapper to launch waybar correctly
4. **No Gap Changes**: Gaps are set once at startup to `0 0 0 0`, then never changed
   - This prevents window layout recalculation
   - Waybar overlays on top of windows instead of pushing them down
   - Result: Zero window movement (no horizontal or vertical resizing)

## Customization

### Adjust Sensitivity

Edit `~/.local/bin/waybar-auto-hide`:

```bash
SHOW_THRESHOLD=40  # Increase for easier triggering (e.g., 50)
HIDE_THRESHOLD=60  # Increase for more stable hiding (e.g., 70)
```


### Adjust Check Interval

For faster/slower response:
```bash
CHECK_INTERVAL=0.1  # 100ms (faster = more CPU, slower = less responsive)
```

## Troubleshooting

### Waybar Doesn't Show on Hover After Omarchy Update

**Problem**: After updating Omarchy, the waybar auto-hide script stopped working. Hovering near the top didn't show waybar.

**Root Cause**: Omarchy uses a wrapper application launcher (`uwsm-app`) to start waybar instead of launching `waybar` directly. The script was trying to start waybar with `waybar &`, but Omarchy expects applications to be launched via `uwsm-app -- waybar`.

**Solution**: Updated the `show_waybar()` function to use `uwsm-app -- waybar &` instead of `waybar &`. This ensures waybar is launched through Omarchy's application management system, which handles proper initialization and environment setup.

**Why It Works Now**:
- `uwsm-app` is Omarchy's unified application launcher wrapper
- It ensures waybar starts with the correct environment variables and settings
- It integrates with Omarchy's application management system
- Without it, waybar may start but not function correctly or may not appear at all

**To Verify Fix**:
1. Check if script is using correct launcher:
   ```bash
   grep "uwsm-app" ~/.local/bin/waybar-auto-hide
   ```
   Should show: `uwsm-app -- waybar &`

2. Manually test waybar start:
   ```bash
   uwsm-app -- waybar &
   ```

3. Check if script is running:
   ```bash
   pgrep -f waybar-auto-hide
   ```

### Waybar Doesn't Show on Hover (General)

1. Check if script is running:
   ```bash
   pgrep -f waybar-auto-hide
   ```

2. Check cursor position:
   ```bash
   hyprctl cursorpos -j | jq '.y'
   ```
   Should be ≤ 40 when at top

3. Manually test waybar start:
   ```bash
   uwsm-app -- waybar &
   ```

4. Check script logs (if debug enabled):
   ```bash
   tail -f /tmp/waybar-debug.log
   ```

### Windows Don't Extend to Top

1. Check current gaps:
   ```bash
   hyprctl getoption general:gaps_out
   ```

2. Manually set gaps:
   ```bash
   hyprctl keyword general:gaps_out "0 0 0 0"
   ```

### Waybar Keeps Restarting

- Increase `HIDE_THRESHOLD` to create a larger buffer zone
- Increase `CHECK_INTERVAL` to reduce sensitivity

## Files Involved

1. **Script**: `~/.local/bin/waybar-auto-hide`
2. **Autostart**: `~/.config/hypr/autostart.conf`
3. **Waybar Config**: `~/.config/waybar/config.jsonc`
4. **Waybar Style**: `~/.config/waybar/style.css`

## Dependencies

- `hyprctl` - Hyprland control utility (comes with Hyprland)
- `jq` - JSON processor (for parsing cursor position)
- `waybar` - Waybar bar process
- `uwsm-app` - Omarchy's unified application launcher wrapper (required to start waybar)
- `pgrep` / `pkill` - Process management (standard Linux tools)

## Notes

- Waybar is completely killed when hidden (not just made transparent)
- Windows extend fully to top when waybar is hidden
- Waybar appears fully opaque when shown (no transparency)
- **Zero window movement** - windows never resize or move when waybar shows/hides
- Waybar overlays on top of windows (doesn't push them down)
- The script runs continuously in the background
- Uses minimal CPU (checks every 100ms)
- Smooth transitions with proper sleep delays

## Alternative Approaches Considered

1. **CSS Opacity**: Tried using CSS opacity but waybar still took up space
2. **Layer Rules**: Tried Hyprland layer rules but opacity doesn't work for layer surfaces
3. **Waybar IPC**: `waybar-msg` doesn't exist in this installation
4. **Gap Changes**: Tried changing `gaps_out` to push windows down, but caused horizontal window movement
5. **Kill/Restart with Overlay**: **Final solution** - Kill/restart waybar, let it overlay on windows
   - No gap changes = no layout recalculation = no window movement
   - Waybar overlays on top when visible, windows stay in place

## References

- Hyprland gaps documentation: https://wiki.hyprland.org/Configuring/Variables/#general
- Waybar documentation: https://github.com/Alexays/Waybar
- Original implementation: Created during Omarchy configuration session

