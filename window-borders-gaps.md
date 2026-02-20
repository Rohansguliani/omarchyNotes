# Window Borders and Gaps (Flush Edges)

## Overview
This document tracks the configuration used to remove all padding and borders around windows in Hyprland, creating a perfectly flush, 0-pixel edge against the physical screen bezel.

## The Problem
By default, Hyprland adds a slight padding (`gaps_out`) around the edges of the screen, and a gap between tiled windows (`gaps_in`). This creates a small visible border even when an app is "maximized" or tiled to the edge.

## The Fix

We uncommented the explicit gap and border rules in the Look and Feel configuration to force everything to zero.

**File**: `~/.config/hypr/looknfeel.conf`

**Configuration Applied**:
```conf
general {
    # No gaps between windows or borders
    gaps_in = 0
    gaps_out = 0
    border_size = 0
}
```

### What these variables do:
1. **`gaps_out = 0`**: Removes the padding between the outer edges of windows and the physical edge of the monitor. This is what makes a window sit perfectly flush against the bezel.
2. **`gaps_in = 0`**: Removes the spacing between two adjacent tiled windows.
3. **`border_size = 0`**: Removes any colored borders drawn by Hyprland around the active/inactive windows.

## Quick Application Command
If you ever want to toggle this on the fly without editing the config file:

```bash
# Set flush edges
hyprctl keyword general:gaps_in 0
hyprctl keyword general:gaps_out 0
hyprctl keyword general:border_size 0
```
