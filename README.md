# shipyard

Central repository for publishing application builds. Each app lives in its own folder with a download page, so builds can be shared with a single link.

Site: <https://veynora.github.io/shipyard/> (GitHub Pages, served from the `main` branch)

## Apps

| App | Platform | Version | Download page | Details |
|---|---|---|---|---|
| **Nudge** | macOS 11+ (Apple Silicon, Intel), Windows 10/11 (x64, ARM64) | 2.3.0 | <https://veynora.github.io/shipyard/nudge/> | [nudge/README.md](nudge/README.md) |

Nudge is a reminder companion: an animated 3D character appears in the window you're working in and tells you what to do. Features, install steps, release history and known limitations are in its [README](nudge/README.md).

## Repository layout

```
shipyard/
├── README.md              this file (overview of the repository)
└── nudge/
    ├── README.md          detailed Nudge guide (features, install, history, publishing)
    ├── index.html         download page (GitHub Pages)
    ├── icon.png           page logo
    ├── favicon.png        page favicon
    └── vX.Y.Z/            one folder per version
        ├── Nudge-X.Y.Z-{arm64,x64}.{dmg,exe}   installers (+ .sha256 each)
        └── release-notes.html                  release notes page
```

## Publishing a new build

Each app has its own steps. For Nudge see [nudge/README.md](nudge/README.md#publishing-a-new-build): copy the installers into a new `nudge/vX.Y.Z/` folder, write the checksums, add the release notes page, update the download page, and merge a pull request into `main`.

## Adding another app

Create a new folder (for example `myapp/`) with its own `README.md`, `index.html` and one folder per version, then add a row to the Apps table. It will be served at `https://veynora.github.io/shipyard/myapp/`.
