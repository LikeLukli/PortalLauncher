# Building Portal Launcher on Arch Linux and CachyOS

These instructions build Portal Launcher from source on Arch Linux or CachyOS. CachyOS uses the same `pacman` package manager and Arch package names used below.

## 1. Install the build dependencies

Update the system and install the compiler, Qt 6 modules, and libraries used by the launcher:

```bash
sudo pacman -Syu --needed \
  base-devel git cmake ninja extra-cmake-modules pkgconf scdoc \
  cmark gamemode libarchive qrencode zlib libxcb xcb-util-cursor tomlplusplus \
  qt6-base qt6-imageformats qt6-networkauth qt6-websockets
```

`base-devel` supplies the standard C/C++ build tools. `extra-cmake-modules` supplies ECM, which CMake requires. The Qt modules listed above provide the Qt 6 components used by the application.

## 2. Get the source

Clone the repository with its submodules:

```bash
git clone --recurse-submodules --branch main \
  https://github.com/LikeLukli/PortalLauncher.git
cd PortalLauncher
```

For an existing checkout, update it and initialize the submodule:

```bash
git pull
git submodule update --init --recursive
```

## 3. Configure and build

The `linux` CMake preset uses Ninja and creates the build directory at `build/`:

```bash
cmake --preset linux
cmake --build --preset linux --config Release
```

If you change dependencies or the Qt installation later, reconfigure with a fresh CMake cache:

```bash
cmake --fresh --preset linux
```

## 4. Run an isolated portable build

Stage the application under a local `preview/` directory, then install the portable launcher script and marker there:

```bash
cmake --install build --config Release --prefix "$PWD/preview"
cmake --install build --config Release --prefix "$PWD/preview" --component portable
./preview/prismlauncher
```

The portable marker makes this copy keep its launcher data beside the preview instead of using the normal user data directory. This does not install or replace a system package.

## Troubleshooting

- **CMake is too old:** Arch and CachyOS are rolling releases; update the system so the installed CMake meets the version required by `CMakePresets.json`.
- **`ECM` is not found:** install `extra-cmake-modules`, then configure again with `cmake --fresh --preset linux`.
- **A Qt component is missing:** confirm the matching Qt 6 package is installed, then configure again with `cmake --fresh --preset linux`.
- **A submodule is empty:** run `git submodule update --init --recursive` from the repository root.

The Linux preset uses the distribution's Qt and native libraries. It is intended for building and previewing on the same system, rather than creating a portable binary for other distributions.
