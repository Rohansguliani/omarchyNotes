# Headless Server & Lid Close Configuration

To allow this machine to function seamlessly as a headless server (such as running OpenCode/Zrok instances), the following configuration changes were made:

## 1. Systemd Lid Switch Ignored
By default, `systemd-logind` forces the computer into `suspend` when the laptop lid is closed. This was overridden so servers continue to run.

A drop-in configuration file was created:
```bash
# /etc/systemd/logind.conf.d/ignore-lid.conf
[Login]
HandleLidSwitch=ignore
HandleLidSwitchExternalPower=ignore
HandleLidSwitchDocked=ignore
```
*Applied via `sudo systemctl kill -s HUP systemd-logind`*

## 2. Hyprland Lid Display Toggling
Since `systemd` no longer suspends the machine, the screen would remain powered on while the lid was shut. To save power and avoid burn-in, Hyprland was configured to manually toggle the internal display.

The following bindings were added to `~/.config/hypr/bindings.conf`:
```hyprland
# Turn off display when lid is closed (but keep processes running)
bindl = , switch:on:Lid Switch, exec, hyprctl keyword monitor "eDP-1, disable"
bindl = , switch:off:Lid Switch, exec, hyprctl keyword monitor "eDP-1, preferred, auto, 1"
```

## 3. Systemd User Lingering (Autostart)
To ensure that background services (like `zrok-prod.service` and `zrok-dev.service`) start automatically when the computer boots—even if no user logs into the graphical desktop—Systemd User Lingering was enabled.

```bash
sudo loginctl enable-linger rohansguliani
```

With this, the server stack launches seamlessly at boot and survives headless operation.