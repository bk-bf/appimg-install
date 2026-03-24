# apkg

A single bash script to install, update, and remove AppImages like a native package manager — no tools, no daemons, no config files.

## What it does

Running `apkg -S foo.AppImage` takes care of all three steps in one command:

1. Copies the AppImage to `~/.local/share/appimages/<name>.AppImage` and makes it executable
2. Creates a symlink at `~/.local/bin/<name>` so it runs from anywhere in the terminal
3. Writes a `.desktop` entry to `~/.local/share/applications/` so it appears in launchers (dmenu, rofi, GNOME, etc.)

When you install a new version later, only the file changes. The symlink and `.desktop` entry both point to the name, not the version, so they never need updating.

## Installation

```bash
curl -fsSL https://raw.githubusercontent.com/bk-bf/appimg-install/main/apkg | install -Dm755 /dev/stdin ~/.local/bin/apkg
```

Make sure `~/.local/bin` is in your `PATH`. If it isn't, add this to your `~/.bashrc` or `~/.zshrc`:

```bash
export PATH="$HOME/.local/bin:$PATH"
```

## Usage

```
apkg -S <file.AppImage> [name]   Install or update an AppImage
apkg -U <file.AppImage> [name]   Same as -S (install from local file)
apkg -R <name>                   Remove a previously installed AppImage
```

### Install

```bash
# Name is inferred from the filename (version + arch stripped automatically)
apkg -S ~/Downloads/mux-0.21.0-x86_64.AppImage
# → installs as "mux"

# Or specify the name explicitly
apkg -S ~/Downloads/mux-0.21.0-x86_64.AppImage mux
```

### Update

Same command, new file. The symlink and `.desktop` stay untouched:

```bash
apkg -S ~/Downloads/mux-0.22.0-x86_64.AppImage mux
# or equivalently
apkg -U ~/Downloads/mux-0.22.0-x86_64.AppImage mux
```

### Remove

```bash
apkg -R mux
```

Removes the AppImage, symlink, and `.desktop` entry.

### Force-refresh the `.desktop` entry

By default the `.desktop` file is not overwritten on update. To regenerate it:

```bash
apkg -S ~/Downloads/mux-0.22.0-x86_64.AppImage mux --overwrite-desktop
```

## Name inference

Version strings and architecture suffixes are stripped automatically:

| Filename | Inferred name |
|---|---|
| `mux-0.21.0-x86_64.AppImage` | `mux` |
| `Obsidian-1.5.3.AppImage` | `obsidian` |
| `JetBrains.Toolbox-2.2.AppImage` | `jetbrains.toolbox` |

Pass an explicit name as the second argument to override.

## File locations

| What | Where |
|---|---|
| AppImage | `~/.local/share/appimages/<name>.AppImage` |
| Symlink | `~/.local/bin/<name>` |
| Desktop entry | `~/.local/share/applications/<name>.desktop` |

## License

MIT
