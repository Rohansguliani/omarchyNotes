# CPU Performance Mode Configuration

## Overview
Configured the system for maximum CPU performance by setting the governor to performance mode and disabling conflicting power management services.

## Problem
On a fresh omarchy build, two power management systems were conflicting:
1. `cpupower.service` - sets governor at boot
2. `power-profiles-daemon` - was overriding it after boot, setting CPUs back to powersave

## Solution

### 1. Masked power-profiles-daemon
Prevents it from interfering with cpupower settings:
```bash
sudo systemctl stop power-profiles-daemon
sudo systemctl mask power-profiles-daemon
```

### 2. Configured cpupower for Performance Mode

**File**: `/etc/default/cpupower`

```bash
governor='performance'
min_freq="3.8GHz"
perf_bias=0
```

### 3. Applied Settings
```bash
sudo cpupower frequency-set -g performance
sudo cpupower frequency-set -d 3.8GHz
sudo cpupower set -b 0
```

## Verification Commands

Check current governor (all CPUs):
```bash
cat /sys/devices/system/cpu/cpu*/cpufreq/scaling_governor | sort | uniq -c
```

Check current frequencies:
```bash
cat /sys/devices/system/cpu/cpu*/cpufreq/scaling_cur_freq | awk '{sum+=$1; count++} END {printf "Average: %.2f GHz\n", sum/count/1000000}'
```

Check service status:
```bash
systemctl status cpupower.service
systemctl is-enabled power-profiles-daemon  # Should show "masked"
```

## Current State
| Setting | Value |
|---------|-------|
| Governor | performance (all 14 CPUs) |
| Min Frequency | 3.8 GHz |
| Perf Bias | 0 (max performance) |
| Turbo Boost | Enabled |
| cpupower.service | enabled |
| power-profiles-daemon | masked |

## Notes on Intel HWP
With modern Intel CPUs using HWP (Hardware P-States), the hardware maintains some control over frequency scaling. Even with these settings, frequencies may vary slightly, but HWP ramps up in microseconds so responsiveness is excellent.

### Optional: Disable HWP Entirely
For true frequency pinning, add to kernel parameters:
```
intel_pstate=passive
```
This gives the OS full control but has trade-offs (more power, more heat).

## Files Modified
- `/etc/default/cpupower` - governor and frequency settings
- Systemd: masked `power-profiles-daemon`

