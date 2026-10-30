# Zephyi Optimizer

A free Windows 10/11 gaming optimizer that only changes what helps your PC - and can undo everything.

**[Download the latest version](https://github.com/itzlizar56-cmd/zephyi-zephyi-optimizer/releases/latest)** - run `Zephyi Optimizer Setup.exe`.
Windows may say "Windows protected your PC" because the installer isn't code-signed: click **More info -> Run anyway**.

## What it does

- **Smart scan** - reads your CPU, GPU, RAM, drive, games and running apps, then picks only the tweaks that help *your* setup and says why.
- **48 tweaks** - services, CPU, RAM & storage, GPU, input & network and a registry cleanup. Every one is verified after it is applied.
- **Tools** - Game Boost, app installer (winget / Chocolatey), Discord debloat, startup manager, health check, driver checker and competitive in-game settings.
- **Safe** - a System Restore point and a full backup before every change. "Undo all" (and uninstalling) puts everything back.

## How it's made

The whole app is plain PowerShell and C# that ships with Windows - there's nothing to download to build it.

| File | What it is |
| --- | --- |
| `ZephyiOptimizer.ps1` | The app. A WPF window (XAML inside the script) plus an "engine" that runs in a background runspace so the window never freezes. |
| `Start ZephyiOptimizer.bat` | Runs the script straight from this folder (for development). |
| `assets/` | The logo (`logo.png`) and icon (`logo.ico`). |
| `installer/Setup.cs` | Install logic: copies the files, shortcuts, Settings > Apps entry, hourly update task. Also `/silent` mode for updates. |
| `installer/SetupUI.cs` | The dark installer window. |
| `installer/Launcher.cs` | `Zephyi Optimizer.exe` - starts the script without a console window, and handles uninstall (with optional "undo all"). |
| `installer/Updater.cs` | `Zephyi Updater.exe` - the background update check run by Task Scheduler. |
| `installer/build.ps1` | Builds `dist/Zephyi Optimizer Setup.exe` and a zip with the C# compiler built into Windows. |
| `installer/publish.ps1` | Builds and creates a GitHub release (needs GitHub CLI). |
| `installer/stats.ps1` | Shows how many times each release was downloaded. |

### Inside `ZephyiOptimizer.ps1`

- **Engine** (`$EngineCode`) - everything that touches Windows. Each tweak is a small list of what it changes (services, registry values, power settings), and the *same* list is used to apply it, to verify it, and to back it up for undo.
- **Backup** - before a value is changed the first time, its original is saved to `C:\ProgramData\ZephyiOptimizer\backup.json`. Undo writes the originals back and deletes registry keys the app created.
- **Window** - XAML for the layout, then PowerShell builds the cards, pages and animations.
- **Updates** - checks the latest GitHub release, verifies the download against GitHub's SHA-256 checksum and that it's really a Zephyi Optimizer installer, then installs it silently.

## Build it yourself

```powershell
# Run the app from source (asks for admin)
.\Start ZephyiOptimizer.bat

# Look around without admin - nothing can be applied
powershell -ExecutionPolicy Bypass -File .\ZephyiOptimizer.ps1 -Preview

# Build the installer and zip into dist\
powershell -ExecutionPolicy Bypass -File .\installer\build.ps1
```

The version number is `$AppVersion` at the top of `ZephyiOptimizer.ps1`. Raise it before building a new release.
