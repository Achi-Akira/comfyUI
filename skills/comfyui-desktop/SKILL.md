---
name: comfyui-desktop
description: Install, configure and troubleshoot ComfyUI Desktop, the packaged Electron app that bundles ComfyUI, the ComfyUI frontend, ComfyUI-Manager and uv, and build it from source. Use when the user asks where ComfyUI Desktop keeps its models, custom nodes, outputs, config or logs, wants to add extra model folders, fix a Desktop install or startup problem, or develop or package the Desktop app.
---

# ComfyUI Desktop

ComfyUI Desktop packages a stable ComfyUI with the ComfyUI frontend, ComfyUI-Manager and `uv`. On startup it installs the Python dependencies with uv and starts the ComfyUI server. It updates itself with stable releases.

This repository is **archived**: it says it was superseded by https://github.com/Comfy-Org/Comfy-Desktop. Tell the user that before they rely on it for new installs or report bugs here.

## Install

- Windows (NVIDIA): NSIS installer from https://download.comfy.org/windows/nsis/x64
- macOS (Apple Silicon): DMG from https://download.comfy.org/mac/dmg/arm64
- User guide: https://docs.comfy.org/installation/desktop

On first run the app asks for a folder for ComfyUI's files: models, inputs, outputs, custom nodes and workflows.

## Where things are

| What | Windows | macOS | Linux |
|---|---|---|---|
| App files | `%LOCALAPPDATA%\Programs\ComfyUI` | `/Applications` | — |
| User settings | `%APPDATA%\ComfyUI` | `~/Library/Application Support/ComfyUI` | `~/.config/ComfyUI` |
| `config.json` (its `basePath` is the ComfyUI files folder) | `%APPDATA%\ComfyUI\config.json` | `~/Library/Application Support/ComfyUI/config.json` | `~/.config/ComfyUI/config.json` |
| `extra_models_config.yaml` | `%APPDATA%\ComfyUI\extra_models_config.yaml` | `~/Library/Application Support/ComfyUI/extra_models_config.yaml` | `~/.config/ComfyUI/extra_models_config.yaml` |
| Logs | `%AppData%\ComfyUI\logs` | `~/Library/Logs/ComfyUI` | `~/.config/ComfyUI/logs` |
| Updater cache | `%LOCALAPPDATA%\comfyui-electron-updater` | — | — |

Logs: `main.log` is the Electron app, and `comfyui_<date>.log` is the ComfyUI server. When something fails, read the newest of each before guessing.

## Common tasks

- **Find the models, custom nodes or outputs folder:** read `basePath` from `config.json`. Models are in `<basePath>/models/<type>`, custom nodes in `<basePath>/custom_nodes`, outputs in `<basePath>/output`.
- **Use models from another folder** (for example, an existing ComfyUI install): add a search path to `extra_models_config.yaml`. Its `base_path` is the Desktop folder by default. Back the file up before editing it, and restart the app after.
- **Install custom nodes:** use ComfyUI-Manager inside the app, or clone into `<basePath>/custom_nodes`, then restart.
- **Startup or dependency errors:** check `comfyui_<date>.log`. The app reinstalls dependencies with uv, so a failing custom node's requirements are a common cause. Disable the newest custom node and restart to test.

Before deleting or moving anything in these folders, confirm with the user: the models folder can hold many GB they'd have to download again.

## Build from source

Needs Python 3.12+ (in a virtual environment), Node 20 (via nvm or nvm-windows) and Yarn 4 (`corepack enable`). On Windows, it also needs Visual Studio 2019 or later with the **Desktop development with C++** workload and the **MSVC Spectre-mitigated libraries** for `node-gyp`.

```bash
yarn install
yarn make:assets      # downloads ComfyUI and other bundled parts into assets/
deactivate            # leave any Python env so it doesn't affect the app
yarn start            # build, launch, and rebuild on changes
yarn make             # build packages and installers
```

`yarn make --windows` cross-builds Windows from Linux.

Before committing, the repo expects `yarn format`, `yarn lint`, `yarn typecheck` and `yarn test:unit`, plus `yarn test:e2e` for UI changes. Never use the `any` type; use `unknown`.

**`NODE_MODULE_VERSION` mismatch** (for example `node-pty` compiled against another Node version): rebuild native modules against Electron with `npx electron-rebuild`, or reinstall with the Node version the project expects.
