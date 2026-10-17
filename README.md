# shipyard

Central repository for publishing application builds. Each app lives in its own folder with a download page, so builds can be shared with a single link.

Site: <https://veynora.github.io/shipyard/> (GitHub Pages, served from the `main` branch)

## Apps

| App | Platform | Version | Download page |
|---|---|---|---|
| **Nudge** | macOS 11+ (Apple Silicon, Intel), Windows 10/11 (x64, ARM64) | 2.0.0 | <https://veynora.github.io/shipyard/nudge/> |

## Nudge

<img src="nudge/icon.png" alt="Nudge icon" width="96" align="right">

A reminder companion for your Mac and Windows PC. When a reminder is due, an animated 3D character appears in the window you're working in (including full-screen apps and other Spaces) and tells you what to do.

**Features**
- Reminder appears where you are, without stealing focus
- One-step resolve: done, snooze, or type a phrase like "after lunch" or "friday 5pm"
- Eight characters (Aria, Celeste, Elara, Luna, Nova, Raven, Scarlet, Valkyrie)
- Personality voices, optional spoken reminders, recurring reminders, brain-dump entry
- Google Calendar and ICS import
- Menu-bar (Mac) / system-tray (Windows) app that works offline; data stays on your computer in `~/.nudge/` (`%USERPROFILE%\.nudge` on Windows)
- Optional AI-written wording with your own Anthropic API key (Settings)

**Download (2.0.0)**

| Platform | File | Size | Checksum |
|---|---|---|---|
| macOS, Apple Silicon | [`nudge/Nudge-2.0.0-arm64.dmg`](nudge/Nudge-2.0.0-arm64.dmg) | 24 MB | [`.sha256`](nudge/Nudge-2.0.0-arm64.dmg.sha256) |
| macOS, Intel | [`nudge/Nudge-2.0.0-x64.dmg`](nudge/Nudge-2.0.0-x64.dmg) | 24 MB | [`.sha256`](nudge/Nudge-2.0.0-x64.dmg.sha256) |
| Windows, 64-bit | [`nudge/Nudge-2.0.0-x64.exe`](nudge/Nudge-2.0.0-x64.exe) | 20 MB | [`.sha256`](nudge/Nudge-2.0.0-x64.exe.sha256) |
| Windows, ARM64 | [`nudge/Nudge-2.0.0-arm64.exe`](nudge/Nudge-2.0.0-arm64.exe) | 19 MB | [`.sha256`](nudge/Nudge-2.0.0-arm64.exe.sha256) |

Previous: 1.0.0 (Python build), [`.dmg`](nudge/Nudge-1.0.0.dmg) and [`.exe`](nudge/Nudge-1.0.0.exe).

```sh
shasum -a 256 ~/Downloads/Nudge-2.0.0-arm64.dmg   # must match the .sha256 file
```
```powershell
Get-FileHash $HOME\Downloads\Nudge-2.0.0-x64.exe -Algorithm SHA256   # must match the .sha256 file
```

**Install (macOS)**
1. Open the DMG for your Mac (arm64 for Apple Silicon, x64 for Intel) and drag **Nudge** into **Applications**.
2. First launch: right-click Nudge → **Open** → **Open**. The build is ad-hoc signed and not yet notarized by Apple, so macOS asks once.
3. If macOS still blocks it:
   ```sh
   xattr -dr com.apple.quarantine /Applications/Nudge.app
   ```
4. Look for the **N** in the menu bar.

**Install (Windows)**
1. Run `Nudge-2.0.0-x64.exe` (`-arm64` on ARM PCs). The build is not code-signed yet, so if SmartScreen says "Windows protected your PC", click **More info** → **Run anyway**.
2. Finish the installer (per-user, no administrator rights needed).
3. Needs the Edge **WebView2 Runtime** (included with current Windows 10/11; the installer downloads it if missing).
4. Look for the **N** in the system tray.

**Requirements**: macOS 11 or later (Apple Silicon or Intel); or Windows 10/11 (x64 or ARM64).

**Uninstall**: macOS: quit Nudge, delete `Nudge.app`. Windows: Settings → Apps → Nudge → Uninstall. Optionally delete `~/.nudge/` (`%USERPROFILE%\.nudge` on Windows) for reminders, settings, logs.

## Repository layout

```
shipyard/
├── README.md              this file
└── nudge/
    ├── index.html         download page (GitHub Pages)
    ├── icon.png           page logo
    ├── favicon.png        page favicon
    ├── Nudge-2.0.0-{arm64,x64}.dmg   macOS installers (+ .sha256 each)
    ├── Nudge-2.0.0-{arm64,x64}.exe   Windows installers (+ .sha256 each)
    └── Nudge-1.0.0.{dmg,exe}         previous version (+ .sha256 each)
```

## Publishing a new build

1. Build the installers in the source project (`applications/nudge/build`): `make -C dmg` (macOS arm64 + x64) and `make -C exe` (Windows x64 + ARM64). They land in `applications/nudge/build/release/`.
2. Copy them here with hyphenated names and write checksums:
   ```sh
   R=../applications/nudge/build/release; V=2.0.0
   for a in arm64 x64; do
     cp $R/Nudge_${V}_$a.dmg nudge/Nudge-$V-$a.dmg
     cp $R/Nudge_${V}_$a.exe nudge/Nudge-$V-$a.exe
   done
   (cd nudge && for f in Nudge-$V-*; do shasum -a 256 $f > $f.sha256; done)
   ```
3. Do this on a branch and open a PR into `main`.
4. Update the version, file names, sizes and SHA-256 values in `nudge/index.html` and in the table above.
5. Merge to `main`. The page updates within a couple of minutes.

> Each release adds roughly 85 MB (four installers at about 20 MB each) to the repo history, and GitHub rejects single files over 100 MB. For frequent releases, consider attaching DMGs to GitHub Releases and pointing the download button there.

## Adding another app

Create a new folder (for example `myapp/`) with its own `index.html` and build files, then add a row to the Apps table. It will be served at `https://veynora.github.io/shipyard/myapp/`.
