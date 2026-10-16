# shipyard

Central repository for publishing application builds. Each app lives in its own folder with a download page, so builds can be shared with a single link.

Site: <https://veynora.github.io/shipyard/> (GitHub Pages, served from the `main` branch)

## Apps

| App | Platform | Version | Download page |
|---|---|---|---|
| **Nudge** | macOS 11+ (Apple Silicon), Windows 10/11 (64-bit) | 1.0.0 | <https://veynora.github.io/shipyard/nudge/> |

## Nudge

<img src="nudge/icon.png" alt="Nudge icon" width="96" align="right">

A reminder companion for your Mac and Windows PC. When a reminder is due, an animated 3D character appears in the window you're working in (including full-screen apps and other Spaces) and tells you what to do.

**Features**
- Reminder appears where you are, without stealing focus
- One-step resolve: done, snooze, or type a phrase like "after lunch" or "friday 5pm"
- Six characters (Robot, Nova, Raven, Scarlet, Elara, Valkyrie) and your own `.glb` models
- Personality voices, optional spoken reminders, recurring reminders, brain-dump entry
- Google Calendar and ICS import
- Menu-bar (Mac) / system-tray (Windows) app that works offline; data stays on your computer in `~/.nudge/` (`%USERPROFILE%\.nudge` on Windows)
- Optional AI-written wording with your own Anthropic API key (Settings)

**Download**
- macOS: [`nudge/Nudge-1.0.0.dmg`](nudge/Nudge-1.0.0.dmg) (51 MB), checksum [`.sha256`](nudge/Nudge-1.0.0.dmg.sha256)
- Windows: [`nudge/Nudge-1.0.0.exe`](nudge/Nudge-1.0.0.exe) (38 MB), checksum [`.sha256`](nudge/Nudge-1.0.0.exe.sha256)
  ```sh
  shasum -a 256 ~/Downloads/Nudge-1.0.0.dmg   # must match the .sha256 file
  ```
  ```powershell
  Get-FileHash $HOME\Downloads\Nudge-1.0.0.exe -Algorithm SHA256   # Windows; must match the .sha256 file
  ```

**Install (macOS)**
1. Open the DMG and drag **Nudge** into **Applications**.
2. First launch: right-click Nudge → **Open** → **Open**. The build is ad-hoc signed and not yet notarized by Apple, so macOS asks once.
3. If macOS still blocks it:
   ```sh
   xattr -dr com.apple.quarantine /Applications/Nudge.app
   ```
4. Look for the **N** in the menu bar.

**Install (Windows)**
1. Run `Nudge-1.0.0.exe`. The build is not code-signed yet, so if SmartScreen says "Windows protected your PC", click **More info** → **Run anyway**.
2. Optionally add a desktop shortcut and start-at-login, then finish the installer.
3. Needs the Edge **WebView2 Runtime** (included with current Windows 10/11; the installer warns if missing).
4. Look for the **N** in the system tray.

**Requirements**: macOS 11 or later, Apple Silicon (M-series), Intel Macs not supported; or Windows 10/11 64-bit.

**Uninstall**: macOS: quit Nudge, delete `Nudge.app`. Windows: Settings → Apps → Nudge → Uninstall. Optionally delete `~/.nudge/` (`%USERPROFILE%\.nudge` on Windows) for reminders, settings, logs.

## Repository layout

```
shipyard/
├── README.md              this file
└── nudge/
    ├── index.html         download page (GitHub Pages)
    ├── icon.png           page logo
    ├── favicon.png        page favicon
    ├── Nudge-1.0.0.dmg    macOS installer
    ├── Nudge-1.0.0.dmg.sha256
    ├── Nudge-1.0.0.exe    Windows installer
    └── Nudge-1.0.0.exe.sha256
```

## Publishing a new build

1. Build the app and DMG in the source project (`development/nudge/dmg-build`, run `make all`).
2. Copy the DMG into the app's folder here and write its checksum:
   ```sh
   cp .../dist/Nudge-<version>.dmg nudge/
   (cd nudge && shasum -a 256 Nudge-<version>.dmg > Nudge-<version>.dmg.sha256)
   ```
3. Windows: build the installer in `development/nudge/win-build` (`make` or `build.bat` on Windows, or `./docker-build.sh` plus the Inno Setup Docker image on a Mac), then:
   ```sh
   cp .../win-build/dist/Nudge-Setup-<version>.exe nudge/Nudge-<version>.exe
   (cd nudge && shasum -a 256 Nudge-<version>.exe > Nudge-<version>.exe.sha256)
   ```
4. Update the version, file names, sizes and SHA-256 values in `nudge/index.html` and in the table above.
5. Commit and push to `main`. The page updates within a couple of minutes.

> Each DMG committed adds roughly 50 MB (and each Windows installer about 38 MB) to the repo history, and GitHub rejects single files over 100 MB. For frequent releases, consider attaching DMGs to GitHub Releases and pointing the download button there.

## Adding another app

Create a new folder (for example `myapp/`) with its own `index.html` and build files, then add a row to the Apps table. It will be served at `https://veynora.github.io/shipyard/myapp/`.
