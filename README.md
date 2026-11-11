<p align="center">
  <img src="start-view.svg" width="112" alt="Start View">
</p>

<h1 align="center">Start View</h1>

<p align="center">The start page of the viewers and tools: the installed ones as tiles, one click starts one</p>

The start page of the viewers and tools: CAD View, PDF View, Part View, Photo View, CIQ View, FITS View,
GED View, SELforge and PC View as tiles, the installed ones with **Start**; a click starts
one in a window of its own.

## Installing

The packages are on the [latest release](https://github.com/ArkadiuszRaj/View-Launcher-release/releases/latest); each carries everything it needs (Node.js
included):

| system | file | how |
|--------|------|-----|
| Windows 10/11 | `start-view-<version>-win64-setup.exe` | run it: installed for you, no administrator rights; the Start menu. Not signed: SmartScreen asks once, **More info → Run anyway** |
| Windows, portable | `start-view-<version>-win64.zip` | unzip anywhere, run `start-view.exe` |
| Debian, Ubuntu | `start-view_<version>_amd64.deb` | `sudo apt install ./start-view_<version>_amd64.deb`; the menu entry, `start-view` on the command line |
| any Linux | `start-view-<version>-x86_64.AppImage` | `chmod +x` and run it |
| macOS (Apple silicon) | `start-view-<version>-macos-arm64.tar.gz` | `tar xzf …`, then `./install.sh` in its folder (`--user`: without the password) |

The SHA-256 of every file is in the notes of the release.

## Updates

Start View looks for a newer release of itself and offers it in its toolbar: **Update Start View to x.y** fetches it, checks it, installs it and starts it again on the same address. The applications it shows offer their own new versions themselves.

## Running it

```sh
start-view                  # opens the browser on the start page
start-view --app            # in a window of its own (Edge, Chrome, Chromium; else the browser)
start-view --no-open        # only the server: open the printed address yourself
start-view --host 0.0.0.0   # the page for the whole LAN: the applications linked from it
start-view --port 0         # on any free port (default: always 8090)
```

The page is always at <http://127.0.0.1:8090/> (a bookmark works); a second `start-view` only
opens it. Started from the menu, it closes 15 s after its last window.

## Using it

- **a tile** per application: its icon, name, version and where it was found, what it opens;
  the keys **1** to **9** start (or open) the tiles in order
- **Start**: the application is started on a free port and opens its own page, in an app
  window when the start page runs in one (`--app`), else in the browser. It lives on its
  own from then on: it stops when its window is closed (15 s later), and survives the start
  page, which remembers it (`running.json`) and shows it running when opened again
- **Open** (once it runs): its page in a new tab; **Stop**: ends it (its pages stop working);
  the log button: what it printed since it started (`~/.cache/start-view/logs/<name>.log`)
- **PC View** is a server, not a page of its own: when its systemd service runs (the `.deb`
  installs one on `:8080`), the tile says *running as a service* and **Open** goes there;
  else **Start** runs it for the start page, which opens its page and ends it when it ends
- an application that is **not installed** is greyed out, with the way to get it: its
  package, or the checkout built next to this one; **On GitHub** opens its latest release
  (else its repository)
- **Download** (when its repository has a release, made by `tools/mk_release.sh`): the
  package of the latest release for this system, the AppImage (Linux), the zip (Windows) or
  the tar.gz (macOS), fetched into `~/.local/share/start-view/apps/<command>/` (Windows:
  `%LOCALAPPDATA%\start-view\apps\<command>\`), kept only when its SHA-256 is the one of the
  release notes; no installation and no administrator. One fetched before and older than the
  latest release gets **Update to x.y**; one installed otherwise (a package, a checkout) only
  a *x.y released* link. The release is looked for first in `<repository>-release` (a public
  repository of the releases only, where `tools/mk_release.sh` publishes when it exists: no
  token needed), then in the repository itself, private for now: the token is `GITHUB_TOKEN`
  (or `GH_TOKEN`), `githubToken` of `apps.json`, else that of `gh auth token` (`gh auth
  login`). A fine-grained token needs only *Contents: read-only* of the repositories. Not
  offered on the page for the LAN
- **Update Start View to x.y** (in the toolbar, when its own repository has a newer release):
  the start page updates itself the way it was installed and starts again on the same port;
  the open pages reload. The AppImage: the new one fetched and renamed over it (the same
  path). The `.deb`: installed with `pkexec apt-get` (the desktop asks for the password;
  without pkexec the page gives the `sudo apt install` command). The Windows installer: its
  `setup.exe` run silently once the old server is gone. macOS: the new tar.gz unpacked and its
  `install.sh` run (for `/opt/start-view` through osascript: macOS asks for the password; none
  for `~/.local/opt/start-view`). Every package is checked against the
  SHA-256 of the release notes. A checkout (or a zip unpacked by hand) is not replaced: the
  toolbar only says *Start View x.y released*. Applications started by the page that it
  would stop anyway (PC View run for it) stop with the update
- **⋯**: the light or dark theme and the language, English or Polish (the browser's at
  first); both remembered by the browser
- the status bar: where the applications open, the configuration file, the version

### Where the applications are looked for

For each application, in this order, the first one found:

1. `command` in `apps.json` (below)
2. the command in `PATH` (`cad-viewer`, `selforge`, …: the `.deb` installs it in `/usr/bin`)
3. `/opt/<command>/bin/<command>` (the `.deb`; on macOS the `install.sh` of the tar.gz, or
   with `--user` `~/.local/opt/<command>/bin/<command>`)
4. Windows: the installer's `%LOCALAPPDATA%\Programs\<command>`
5. the package fetched from the GitHub Release (**Download**, above)
6. a checkout next to this one (and in the folders of `checkouts` in `apps.json`), built:
   `../CAD-View/bin/cad-viewer.js` with its `dist/`, run with the Node.js of the start page
7. Linux: `<command>-*.AppImage` in `~/Applications`, `~/.local/bin`, `~/bin`, `~/Downloads`

The version shown is the application's own `--version`. The look-up is repeated on every
refresh of the page (the ⟳ button), so an application installed meanwhile appears.

### Configuration (optional)

`~/.local/share/start-view/apps.json` (Windows: `%APPDATA%\start-view\apps.json`):

```json
{
  "checkouts": ["/home/arek/src"],
  "apps": {
    "pc-view": { "url": "http://nas.local:8080/" },
    "cad-viewer": { "command": "/srv/cad-view/bin/cad-viewer" },
    "selforge": { "command": ["/usr/bin/node", "/srv/SELforge/bin/selforge.js"], "args": ["--token", "x"] },
    "photo-viewer": { "hidden": true }
  },
  "githubToken": "github_pat_…"
}
```

- `checkouts`: more folders that hold the checkouts (`CAD-View`, `SELforge`, …)
- `command`: the program (or the program and its first arguments) of an application, before
  any look-up; `args`: more arguments, after the port and the address
- `url`: an application that already runs (a service on another computer): the tile only
  links to it
- `hidden`: not on the page
- `githubToken`: for the releases of the private repositories, when neither `GITHUB_TOKEN`
  nor `gh` gives one

### The page for the LAN

With `--host 0.0.0.0` (or an address of the computer) the start page is served to the
network and behaves as a server: the applications are started without a browser
(`--no-open`), bound to the same address, and the tiles link to them (`/go/<command>`,
which also starts one that is not running: a bookmark); **Stop** ends one, and all of them
end with the start page. The applications are then reachable from the network too: use it
on a trusted LAN (SELforge gives a shell; its address carries its token).

## Licence

Copyright © 2026 Arkadiusz Raj (hijax_pl). GPL-3.0-only (`LICENSE`).

