# debian-spacefun

Apply SpaceFun, the default theme from Debian 6.0 "Squeeze", to a modern
Debian system, from the GRUB menu through to the desktop.

Debian still ships all the SpaceFun artwork in `desktop-base`, but nothing
ties the pieces together: GRUB, Plymouth, the login screen and the desktop
are each configured separately. This script points each of them at the
artwork Debian already installed. It mostly uses `desktop-base`'s own
`update-alternatives` entries, so package upgrades won't undo it, and it
doesn't copy any files into `/boot`.

## Usage

```sh
sudo ./spacefun install     # theme, GRUB menu, Plymouth splash, login screen
./spacefun desktop          # your desktop wallpaper (run as yourself, not sudo)
./spacefun status
```

Then reboot. To undo:

```sh
sudo ./spacefun restore
./spacefun desktop --restore
```

Put `-n` before any command to see what it would change without changing
anything, e.g. `./spacefun -n install`.

## What `install` does

| Stage    | How                                                                  |
|----------|----------------------------------------------------------------------|
| Theme    | `update-alternatives --set desktop-theme …/spacefun-theme`, plus the background, lockscreen and login-background alternatives |
| GRUB     | `desktop-grub` alternative → `grub-16x9.png` (or `grub-4x3.png` on 4:3 / 5:4 screens), then `update-grub` |
| Plymouth | `plymouth-set-default-theme spacefun`, then `update-initramfs -u -k all` |
| Splash   | adds `splash` to the kernel command line via `/etc/default/grub.d/spacefun.cfg`, if it's missing |
| Login    | LightDM: Debian's GTK greeter already uses `login-background.svg`, which now follows SpaceFun. An explicit `background=` in `/etc/lightdm/lightdm-gtk-greeter.conf` is commented out (with a backup). SDDM/GDM follow the alternative. |

It installs `desktop-base` and `plymouth` if they're missing, and is safe to
run more than once: anything already set to SpaceFun is left alone.

The first `install` records the previous settings in `/var/lib/spacefun/`.
`restore` puts those back. If you set up parts of SpaceFun by hand before
running `install`, those parts count as the "previous" settings.

`desktop` supports MATE, GNOME, Cinnamon and Xfce, and saves your previous
wallpaper in `~/.local/state/spacefun/`. KDE is also supported, but its
previous wallpaper can't be saved, so `desktop --restore` can't undo it.

## Requirements

Debian with `desktop-base` (tested on Debian 13 "Trixie" with MATE and
LightDM). Ubuntu doesn't package the SpaceFun artwork, so it isn't supported.

## License

MIT. See [LICENSE](LICENSE). The SpaceFun artwork itself is not part of this
repository; it comes from Debian's `desktop-base` package under its own license.
