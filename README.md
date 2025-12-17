# 🎨 PluginStyler

[![Platform](https://img.shields.io/badge/platform-Windows%2010%20%7C%2011-blue)](https://www.microsoft.com/windows)
[![.NET](https://img.shields.io/badge/.NET-8.0-purple)](https://dotnet.microsoft.com/)
[![Release](https://img.shields.io/github/v/release/kasnowww147/PluginStyler)](../../releases)

[English](#english) | [中文 (Chinese)](#chinese)

---

<a name="english"></a>
## 🇺🇸 English Description

**PluginStyler** is a specialized, high-performance Windows utility designed for music producers using **Bitwig Studio**, **Ableton Live**, and **Cockos Reaper**.

Unlike generic window managers, PluginStyler operates at the Win32 API level, leveraging the **Desktop Window Manager (DWM)** to modernize floating plugin (VST/CLAP) windows. It injects **Mica material** (Windows 11), forces **Dark Mode**, and applies custom accent borders, ensuring third-party plugins integrate visually with your DAW's native aesthetic.

> **Note:** This repository serves as the official distribution channel and issue tracker for PluginStyler.

### ✨ Key Features

* **🪟 Modern Window Styling**
    * **Mica & Dark Mode:** Forces Windows 11 "Mica" backdrop effects and Immersive Dark Mode on plugin containers.
    * **Geometry Control:** Customizable corner radius (Square, Small, or Round) via `DWMWA_WINDOW_CORNER_PREFERENCE`.
    * **Clean Aesthetics:** Option to hide native title bar text (Caption & Icon) for a minimalist look.
* **🎨 Dynamic Coloring**
    * **State Awareness:** Distinct visual themes for **Active** (focused) and **Inactive** windows.
    * **Rainbow Mode:** A dedicated, threaded render loop (**RainbowManager**) providing smooth RGB border cycling with adjustable saturation — **zero impact on the main UI thread**.
    * **Custom Palettes:** Persist custom color configurations using a modern WebView2-based UI.
* **⚡ Architecture & Performance**
    * **Smart PID Caching (TTL):** Implements a robust caching mechanism with Time-To-Live (TTL) logic to minimize handle opening overhead. Identification of plugin processes (e.g., `pluginhost.exe`, `reaper_host.exe`) happens with **negligible CPU usage (~0%)**.
    * **Anti-Flicker Engine:** Uses `DwmFlush()` and early-stage window hooking (`EVENT_OBJECT_CREATE`) to prime window styles immediately, significantly reducing the "white flash" when opening new plugins.
    * **Intelligent Filtering:** Specifically targets plugin containers while strictly excluding main DAW windows (e.g., filtering Ableton Live Window Class or Reaper's `REAPERwnd`).
* **👻 Transparency Control**
    * **Layered Attributes:** Independent opacity settings for active vs. inactive states using `SetLayeredWindowAttributes`, allowing for "see-through" plugins when multitasking.

### 📥 Installation

1.  Navigate to the [Releases](../../releases) page.
2.  Download the latest `PluginStyler.zip`.
3.  Extract the archive to a permanent location (e.g., `C:\Tools\PluginStyler`).
4.  Run `PluginStyler.exe`.
5.  *(Optional)* Enable "Run on Startup" in the settings.

### ⚠️ Compatibility

* **Supported DAWs:**
    * **Bitwig Studio:** Fully supported (targets `pluginhost` / `bitwigpluginhost`).
    * **Ableton Live:** Supported (intelligent filtering excludes the main Suite/Standard window).
    * **Cockos Reaper:** Supported (excludes main `REAPERwnd`, targets VST bridges).
* **OS Requirements:**
    * **Windows 11 (Build 22000+):** Required for **Mica effects** and rounded corners.
    * **Windows 10 (1809+):** Supports **Custom Colors**, Borders, and **Transparency** only.
* **Limitations:**
    * Plugins utilizing completely custom non-client area drawing (Client-Side Decoration) may not accept DWM border coloring.

### 💬 Feedback & Support

If you encounter any bugs or have suggestions for new features, please submit an issue on the [Issues](../../issues) page.

---

<a name="chinese"></a>
## 🇨🇳 中文介绍

**PluginStyler** 是一款专为 **Bitwig Studio**、**Ableton Live** 和 **Cockos Reaper** 用户打造的高性能 Windows 窗口美化工具。

不同于通用的窗口管理软件，PluginStyler 深入 Win32 API 底层，通过调用 **桌面窗口管理器 (DWM)** 将现代化的 UI 元素注入到悬浮的插件（VST/CLAP）窗口中。它支持开启 **Mica (云母)** 材质（Win11）、强制 **深色模式** 以及自定义边框颜色，使第三方插件在视觉上与 DAW 完美融合，不仅美观且极具沉浸感。

> **说明：** 本仓库是 PluginStyler 的官方发布下载页及问题反馈中心。

### ✨ 核心功能

* **🪟 高级窗口样式**
    * **Mica & 深色模式：** 为插件窗口启用 Windows 11 原生的 "Mica" 背景效果及沉浸式深色模式。
    * **几何控制：** 可自定义窗口圆角风格（直角、小圆角或大圆角），通过 `DWMWA_WINDOW_CORNER_PREFERENCE` 实现。
    * **极简标题：** 支持隐藏原生标题栏的文字与图标，提供更清爽的视觉体验。
* **🎨 动态色彩管理**
    * **状态感知：** 分别定义 **激活 (Active)** 和 **非激活 (Inactive)** 窗口的视觉主题，一目了然。
    * **RGB 彩虹模式：** 基于独立线程 (**RainbowManager**) 的动态 RGB 颜色循环系统，支持饱和度调节，**完全不占用主 UI 线程资源**。
    * **WebView2 界面：** 基于现代 Web 技术构建的配置界面，支持保存和管理自定义色板。
* **⚡ 架构与性能优化**
    * **智能 PID 缓存 (TTL)：** 内置带有生存时间 (TTL) 的进程 ID 缓存机制，极大减少了 `OpenProcess` 等系统调用的频率，确保对音频处理 **近乎 0% 的 CPU 占用**。
    * **防闪烁引擎：** 结合 `EVENT_OBJECT_CREATE` 钩子与 `DwmFlush()` 指令，在窗口渲染的第一帧前强制应用样式，有效消除打开插件时的“白屏闪烁”现象。
    * **精准过滤：** 智能识别插件宿主进程（如 Bitwig 的 `pluginhost`），同时严格排除 DAW 主界面（如自动过滤 Ableton Live 主窗口或 Reaper 的 `REAPERwnd`），防止误操作。
* **👻 透明度控制**
    * **分层窗口属性：** 独立控制激活与非激活窗口的透明度，支持非焦点窗口半透明，提升多任务工作流的可视性。

### 📥 安装指南

1.  前往 [Releases](../../releases) 页面。
2.  下载最新的 `PluginStyler.zip` 压缩包。
3.  解压到任意永久文件夹（例如 `D:\Tools\PluginStyler`）。
4.  运行 `PluginStyler.exe`。
5.  *(可选)* 在设置中开启 "开机自启"。

### ⚠️ 兼容性说明

* **支持的 DAW：**
    * **Bitwig Studio：** 完美支持（针对 `pluginhost` / `bitwigpluginhost`）。
    * **Ableton Live：** 支持（智能算法自动排除 Live Suite/Standard 主窗口）。
    * **Cockos Reaper：** 支持（排除主程序 `REAPERwnd`，仅针对 VST 桥接窗口）。
* **操作系统要求：**
    * **Windows 11 (Build 22000+)：** 支持所有功能（含 **Mica 云母材质**、圆角修改）。
    * **Windows 10 (1809+)：** 支持 **自定义颜色** 和 **透明度**。（受限于系统内核，Win10 无法显示 Mica 效果）。
* **已知限制：**
    * 部分采用完全自绘标题栏（Client-Side Decoration / 非标准 Windows 边框）的插件可能无法被 DWM 强制着色。

### 💬 反馈与支持

如果您在使用过程中遇到任何问题或有功能建议，请前往 [Issues](../../issues) 页面提交反馈。

---

## © Copyright

Copyright © 2025. All Rights Reserved.
