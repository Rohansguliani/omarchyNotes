# Helium Browser Installation

## Overview
Installed Helium browser - a privacy-focused, lightweight web browser based on Chromium with built-in ad-blocking.

## Installation

### AUR Package
```bash
yay -S helium-browser-bin --noconfirm
```

If sudo fails during yay install, manually install the built package:
```bash
sudo pacman -U ~/.cache/yay/helium-browser-bin/helium-browser-bin-*.pkg.tar.zst --noconfirm
```

## Package Details
- **Package**: `helium-browser-bin`
- **Version**: 0.7.7.1
- **Size**: ~472 MiB installed

## Locations
- **Executable**: `/usr/bin/helium-browser`
- **Desktop Entry**: `/usr/share/applications/helium-browser.desktop`

## Launch
- From app launcher: Search "Helium"
- From terminal: `helium-browser`

## Links
- **AUR**: https://aur.archlinux.org/packages/helium-browser-bin
- **GitHub**: https://github.com/nichestream/nichestream-nichestream
- **Website**: https://helium.computer

## Optional Dependencies (auto-satisfied)
- `pipewire` - WebRTC desktop sharing under Wayland ✓
- `gtk4` - GTK4 IME for Wayland ✓
- `org.freedesktop.secrets` - password storage on GNOME/Xfce ✓
- `kwallet` - password storage on Plasma ✓
- `upower` - Battery Status API ✓

