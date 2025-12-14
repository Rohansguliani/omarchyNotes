# Fn and Super Key Swap Attempt

## Overview
Attempted to swap the Fn key and Super key functionality using keyd key remapping daemon. The Fn key was not detectable by the OS, making a full swap impossible.

## Goal
- Make Fn key act as Super key
- Make Super key act as Fn key

## Method: keyd

### Installation
```bash
sudo pacman -S keyd
sudo systemctl enable keyd
sudo systemctl start keyd
```

### Configuration File
**Location**: `/etc/keyd/default.conf`

### Attempts Made

#### Attempt 1: Using `fn`
**Config**:
```
[ids]

[main]
leftmeta = fn
fn = leftmeta
```
**Result**: ❌ Did not work

#### Attempt 2: Using `function`
**Config**:
```
[ids]

[main]
leftmeta = function
function = leftmeta
```
**Result**: ❌ Did not work

#### Attempt 3: Using `leftfn`
**Config**:
```
[ids]

[main]
leftmeta = leftfn
leftfn = leftmeta
```
**Result**: ❌ Did not work

## Diagnosis: keyd Monitor

Ran `sudo keyd -m` to see what keys keyd detects when pressed.

**What keyd detected**:
- `wakeup` - System wake events
- `numlock` - Num Lock key
- `kpminus` - Keypad minus
- `leftmouse` - Mouse clicks
- Various touchpad events

**What keyd did NOT detect**:
- No `fn`, `function`, `leftfn`, or any Fn-related key codes
- Fn key presses produced no detectable events

## Root Cause

The Fn key is **not detectable by the OS** - it's handled at the **hardware/firmware level** on most laptops. This means:

1. ✅ **Can do**: Make Super key act like Fn (one-way remap)
2. ❌ **Cannot do**: Make Fn key act like Super (Fn never sends scancodes to OS)

## Current Status

**keyd is installed and running**:
- Service: `keyd.service`
- Status: Active and enabled
- Config file: `/etc/keyd/default.conf` (currently set to `leftfn`)

**Current config** (not working):
```
[ids]

[main]
leftmeta = leftfn
leftfn = leftmeta
```

## Alternative Solutions

### Option 1: One-Way Remap (Super → Fn)
Make Super key act like Fn, but Fn stays as-is:
```
[ids]

[main]
leftmeta = fn
```
**Note**: This would require Fn to be detectable, which it isn't.

### Option 2: BIOS/UEFI Firmware Remapping
Some laptops support hardware-level key remapping:
- **Framework laptops**: Have keyboard configurator
- **System76**: Support firmware remapping
- **Some ThinkPads**: BIOS settings for Fn key behavior
- **Generic**: Check BIOS/UEFI settings for "Fn key swap" or "Function key behavior"

### Option 3: Use Different Key
Remap another detectable key (e.g., Menu key, Right-click key) to act as Super:
```
[ids]

[main]
menu = leftmeta
```

### Option 4: Hyprland Bindings Workaround
Instead of swapping keys, remap all Super bindings to use a different key:
- Change all `SUPER` bindings in Hyprland config to use `ALT` or another modifier
- Not a true swap, but achieves similar result

## Files Involved

1. **keyd config**: `/etc/keyd/default.conf`
2. **keyd service**: `/usr/lib/systemd/system/keyd.service`
3. **keyd logs**: `journalctl -u keyd`

## Commands Reference

**Check keyd status**:
```bash
sudo systemctl status keyd
```

**Restart keyd after config changes**:
```bash
sudo systemctl restart keyd
```

**Monitor key presses** (to see what keyd detects):
```bash
sudo keyd -m
# Press keys and see what codes they produce
# Press Ctrl+C to stop
```

**View keyd logs**:
```bash
sudo journalctl -u keyd -f
```

**Edit config**:
```bash
sudo nano /etc/keyd/default.conf
# After editing, restart: sudo systemctl restart keyd
```

## Notes

- The Fn key on most laptops is a **hardware modifier** that changes what other keys do (F1-F12 functions, brightness, volume, etc.)
- It doesn't send its own scancode to the OS
- This is by design - Fn is meant to modify other keys, not be a standalone key
- Some gaming keyboards and custom keyboards DO expose Fn as a detectable key
- Framework laptops have a special keyboard configurator that can remap Fn at firmware level

## Next Steps

1. **Check BIOS/UEFI** for Fn key remapping options
2. **Check laptop manufacturer tools** (Framework configurator, etc.)
3. **Consider one-way remap** if Super→Fn is the main goal
4. **Use alternative key** if full swap isn't critical

## References

- keyd documentation: `man keyd` or `/usr/share/doc/keyd/`
- keyd GitHub: https://github.com/rvaiya/keyd
- Framework keyboard configurator: https://github.com/FrameworkComputer/linux-docs

