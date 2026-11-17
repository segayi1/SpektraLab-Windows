# SpektraLab for Windows

**English** · [简体中文](README.zh-CN.md)

A film-emulation darkroom for Windows. Open a RAW, pick a film and a paper, and get a physically
modelled print: light traced through 81 wavelengths of the film's dye layers and the paper's, with
grain, halation, DIR couplers and the enlarger's filter pack, at full resolution.

It is a native application: a Qt Quick interface over a C++/Vulkan engine. Nothing runs through
Python, Node, a browser or a subprocess renderer, and your RAWs and their folders are never written.

This is the Windows front end and the Vulkan back end of
[SpektraLab](https://github.com/JamesQiu2005/SpektraLab), the film darkroom for the Mac. The
film process model, the 28 measured film and paper profiles, the print LUTs and the C ABI are
shared with it; the Mac app and its Metal kernels are not built here. This repository is a port,
not the original project.

## What it does

- **Spectral, not a LUT.** Light is traced through 81 wavelengths, the film's dye layers and the
  paper's, the way a darkroom does it. Colour negative, slide, cine negative and print film, colour
  and black-and-white papers.
- **Physical units.** Tell it the negative is 135 or 120 and grain, halation and coupler diffusion
  scale with the frame in micrometres.
- **Develop and Edit.** The film and the paper; the decode (film exposure, white balance, and a
  curve on the scene before it reaches the film); the format; and the effects (grain, halation,
  scatter, glare, couplers). Edit works on the finished print: brightness, contrast, tone, curves,
  HSL, channels, colour wheels, vibrance, saturation, temperature and vignette.
- **Film Edge.** The frame on its own strip — rebate, perforations, each stock's edge print and
  frame numbers — in 135, half frame, XPan, 645 to 6×9, 6×12 and 6×17, and the five cine gauges:
  Super 8, Super 16, 35 mm 4-perf, and 65 mm 5- and 15-perf; on Super 8 and 16 mm the neighbouring
  frames' perforations join the strip.
- **A library.** Linked folders and persistent catalogues, ratings, colour labels, collections, a
  trash, variants and 135 half-frame pairs, with the settings of one photo copied onto many.
- **Crop and straighten.** Free and ratio crops with quarter turns; post-render geometry, so the
  emulsion does not run again.
- **Export.** TIFF at 16 bits, PNG at 8 or 16, JPEG and HEIF at 8, in sRGB, Adobe RGB, Display P3 or
  ProPhoto RGB, with saved recipes and serial batch export. HEIF is sRGB only and needs the Windows
  HEIF Image Extension.

## Requirements

Windows 10 or 11, x64, with a Vulkan driver from the installed GPU. The front end uses the official
Qt 6.11.3 MinGW SDK and its MinGW-w64 13.1 compiler; do not link it against an earlier GCC 15 build.
CMake 3.25 or later.

## Build

From the repository root:

```powershell
./windows_UI/setup-qt.ps1 -DependencyDirectory ../deps       # pinned Qt, SHA256 per file
./windows_UI/setup-glslang.ps1 -DependencyDirectory ../deps   # glslang and the Vulkan headers
./windows_UI/build.ps1 -SourceDirectory . -BuildDirectory ./build/qt -RunTests
```

The bootstrap needs only PowerShell and Windows' own `tar`, downloads about 278 MB the first time,
and lives beside the checkout in `../deps`. `-Fresh` discards an existing CMake configuration;
`-Configuration Debug` builds the app for debugging with the released Qt runtime. The output
`build/qt/app/` is self-contained: keep its DLLs, QML plugins, resources and notices together.

`engine/WINDOWS.md` covers the engine's own CMake build, LibRaw and glslang;
`windows_UI/README.md` is the front end in detail.

## Test

`build.ps1 -RunTests` runs the engine's 19 Windows tests after the build. They are CPU-only: hosted
CI has no Vulkan driver, so GPU acceptance runs on a machine with one.

## What is not here yet

- The preview is 8-bit SDR. Wide-gamut and HDR display validation, colour calibration, the
  histogram, split comparison and perspective geometry are open.
- Nikon HE/HE* is rejected by the bundled LibRaw build.
- The cine gauges are checked for layout and geometry, not pixel-for-pixel against the Mac's Metal
  render.
- There is no installer; `build/qt/app/` is the application.

## Attribution and licence

**This repository is a Windows port of SpektraLab, which is itself derived from spektrafilm.** The
original project and its Mac app are at
[JamesQiu2005/SpektraLab](https://github.com/JamesQiu2005/SpektraLab). The engine
descends from [spektrafilm](https://github.com/andreavolpato/spektrafilm) by Andrea Volpato
(GPL-3.0-or-later): the film process model, its 28 measured colour profiles and the print LUTs are
spektrafilm's. SpektraLab added the Metal implementation, the Mac app and its own black-and-white
profiles; this repository adds the Vulkan back end and the Qt Quick front end. `license/NOTICE.md`
records what was derived and what was changed.

| | |
|---|---|
| the app and the engine | GPL-3.0-or-later (`license/LICENSE`) |
| spektrafilm's film and paper profiles, the print LUTs | CC BY-SA 4.0 (`license/SPEKTRAFILM_LICENSE.txt`) |
| metal-cpp | Apache-2.0 |
| spklib, the library helper | MIT, shipped as a prebuilt helper binary |

SpektraLab™ and the SpektraLab icon are managed by Guangzhou Songzi Intelligent Technology Services
Co., Ltd. This is an unofficial Windows port, not a release of that project; if you publish a build
for other people, even a free one, give it its own name and icon and say plainly that it is based on
SpektraLab. SpektraLab and spklib are not affiliated with or endorsed by spektrafilm or Capture One;
film and paper names are their owners' trademarks.
