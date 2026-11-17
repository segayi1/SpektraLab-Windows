# SpektraLab Windows 版

[English](README.md) · **简体中文**

Windows 上的胶片暗房。打开一张 RAW，选好胶片和相纸，得到一张按物理过程建模的照片：光线在 81 个波长上穿过胶片与相纸各自的染料层，颗粒、光晕、DIR 成色剂、放大机滤色一样不少，全分辨率渲染。

这是原生应用：界面用 Qt Quick，渲染用 C++/Vulkan 引擎。运行时不经过 Python、Node、浏览器或子进程渲染器，RAW 文件及其所在文件夹不会被写入。

本仓库是 Mac 版 [SpektraLab](https://github.com/JamesQiu2005/SpektraLab) 的 Windows 前端与 Vulkan 后端。胶片过程模型、28 款实测胶片与相纸配置、放大 LUT 与 C ABI 都与它共用；Mac 应用及其 Metal 着色器不在本仓库构建。本仓库是移植版，不是原项目。

## 功能

- **光谱计算，不是 LUT。** 光线在 81 个波长上穿过胶片与相纸的染料层，按暗房的方式计算。彩色负片、反转片、电影负片与正片，彩色与黑白相纸。
- **物理单位。** 告诉它底片是 135 还是 120，颗粒、光晕与成色剂扩散就按画幅以微米为单位缩放。
- **冲洗与编辑。** 胶片与相纸；解码（胶片曝光、白平衡，以及到达胶片之前对场景的曲线）；画幅；效果（颗粒、光晕、散射、眩光、成色剂）。编辑作用于成片：亮度、对比、色调、曲线、HSL、通道、色轮、自然饱和度、饱和度、色温与暗角。
- **片边。** 把画面印在自己的片基上——片边、齿孔、各型号的边缘字符与帧号——覆盖 135、半格、XPan、645 至 6×9、6×12、6×17，以及五种电影画幅：Super 8、Super 16、35 mm 4 齿孔、65 mm 5 齿孔与 15 齿孔；Super 8 与 16 mm 上还带相邻画幅的齿孔。
- **图库。** 链接文件夹与可持久化的目录、评分、色标、收藏、回收站、版本与 135 半格对，并可把一张照片的设置复制到多张。
- **裁切与校正。** 自由与固定比例裁切、四分之一转；裁切作用于渲染之后，不会重跑乳剂。
- **导出。** 16 位 TIFF、8/16 位 PNG、8 位 JPEG 与 HEIF，色彩空间可选 sRGB、Adobe RGB、Display P3 或 ProPhoto RGB，支持导出配方与串行批量导出。HEIF 仅支持 sRGB，且需要 Windows 的 HEIF 图像扩展。

## 运行要求

Windows 10 或 11，x64，显卡驱动提供 Vulkan。界面使用官方 Qt 6.11.3 MinGW SDK 及其 MinGW-w64 13.1 编译器，不要与更早的 GCC 15 构建混用。CMake 3.25 或更高版本。

## 构建

在仓库根目录执行：

```powershell
./windows_UI/setup-qt.ps1 -DependencyDirectory ../deps       # 固定版本的 Qt，逐文件校验 SHA256
./windows_UI/setup-glslang.ps1 -DependencyDirectory ../deps   # glslang 与 Vulkan 头文件
./windows_UI/build.ps1 -SourceDirectory . -BuildDirectory ./build/qt -RunTests
```

引导脚本只需要 PowerShell 与 Windows 自带的 `tar`，首次下载约 278 MB，依赖放在检出目录旁的 `../deps`。`-Fresh` 丢弃已有的 CMake 配置；`-Configuration Debug` 以调试配置构建应用，同时部署发行版 Qt 运行时。产物 `build/qt/app/` 是自包含的：DLL、QML 插件、资源与声明文件要整套保留。

引擎自身的 CMake 构建、LibRaw 与 glslang 见 `engine/WINDOWS.md`；界面细节见 `windows_UI/README.md`。

## 测试

`build.ps1 -RunTests` 在构建后运行引擎的 19 项 Windows 测试。这些测试只用 CPU：托管 CI 没有 Vulkan 驱动，GPU 验收在有显卡的机器上进行。

## 尚未具备

- 预览为 8 位 SDR。宽色域与 HDR 显示验证、色彩校准、直方图、分屏对比与透视几何仍未完成。
- 内置 LibRaw 拒绝 Nikon HE/HE*。
- 五种电影画幅只校验了布局与几何，尚未与 Mac 的 Metal 渲染逐像素比对。
- 没有安装程序；`build/qt/app/` 就是应用。

## 致谢与许可

**本仓库是 SpektraLab 的 Windows 移植版，而 SpektraLab 派生自 spektrafilm。** 原项目及其 Mac 应用见 [JamesQiu2005/SpektraLab](https://github.com/JamesQiu2005/SpektraLab)。引擎源自 Andrea Volpato 的 [spektrafilm](https://github.com/andreavolpato/spektrafilm)（GPL-3.0-or-later）：胶片过程模型、28 款实测配置与放大 LUT 都来自 spektrafilm；SpektraLab 增加了 Metal 实现、Mac 应用与它自己的黑白胶片配置；本仓库增加了 Vulkan 后端与 Qt Quick 前端。派生与改动记录在 `license/NOTICE.md`。

| | |
|---|---|
| 应用与引擎 | GPL-3.0-or-later（`license/LICENSE`） |
| spektrafilm 的胶片与相纸配置、放大 LUT | CC BY-SA 4.0（`license/SPEKTRAFILM_LICENSE.txt`） |
| metal-cpp | Apache-2.0 |
| spklib，图库助手 | MIT，以预编译的助手二进制形式提供 |

SpektraLab™ 与 SpektraLab 图标由广州松子智能科技服务有限公司管理。本仓库是非官方 Windows 移植版，不是该项目的发行版；若要对外发布构建，即使免费，也请使用自己的名称与图标，并明确说明基于 SpektraLab。SpektraLab 与 spklib 与 spektrafilm、Capture One 无关联、未获其背书；胶片与相纸名称是其各自所有者的商标。
