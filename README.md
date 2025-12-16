# 🎨 PluginStyler

[![License](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)
[![Platform](https://img.shields.io/badge/platform-Windows%2011-blue)](https://www.microsoft.com/windows)
[![.NET](https://img.shields.io/badge/.NET-8.0-purple)](https://dotnet.microsoft.com/)

[English](#english) | [中文 (Chinese)](#chinese)

---

<a name="english"></a>
## 🇺🇸 English

### Overview

**PluginStyler** is a specialized Windows desktop utility designed to modernize the visual experience of plugin windows in **Bitwig Studio** and **Ableton Live**.

It leverages the **Desktop Window Manager (DWM)** API to inject Windows 11 visual effects—such as Mica backdrops, custom accent colors, and rounded corners—into third-party plugin containers, creating seamless visual consistency with the host DAW.

This repository contains the **C# backend implementation**, handling low-level OS interactions. The user interface is rendered via an embedded **WebView2** component serving pre-built web resources.

### ✨ Features

#### 🪟 Window Styling
* **Mica Backdrop:** Applies the native Windows 11 Mica material to plugin frames (Requires Windows 11 Build 22000+).
* **Corner Radius Control:** Toggle between Square, Small, or Round window corners.
* **Minimalist Title Bar:** Option to hide native title bar text via `SetWindowThemeAttribute` for a cleaner look.

#### 🎨 Color Management
* **State Awareness:** Distinct color themes for **Focused** and **Unfocused** windows.
* **Rainbow Mode:** Animated HSL-based color cycling for window borders with adjustable saturation.
* **Preset System:** JSON-based configuration system to save, load, and persist custom themes.

#### 👻 Transparency
* **Independent Opacity:** Separate alpha channel values for active and inactive states.
* **Layered Windows:** Utilizes `WS_EX_LAYERED` and `SetLayeredWindowAttributes` for high-performance transparency.

#### ⚙️ Performance & Architecture
* **Selective Targeting:** Uses strict process path and window class filtering to target only specific plugin hosts (e.g., Bitwig `pluginhost`).
* **Zero Audio Interference:**
    * **Debouncing:** 150ms delay on resize events to minimize API calls.
    * **Caching:** Process path caching (30s TTL) and config hash verification to skip redundant updates.
    * **Async Processing:** Background work queue pattern ensures the UI thread is never blocked.

### 🛠️ Technical Architecture

#### Core Components

| Component | Responsibility |
| :--- | :--- |
| **`Form1.cs`** | Main window container, WebView2 host, and System Tray management. |
| **`ConfigService`** | Configuration persistence (JSON) and Registry operations (Startup). |
| **`ThemeService`** | DWM attribute injection (`DwmSetWindowAttribute`, `SetWindowThemeAttribute`). |
| **`RainbowManager`** | Timer-based HSL color animation with parallel window updates. |
| **`WindowFilterService`** | Process identification logic (e.g., distinguishing Plugin windows from Main DAW). |

#### Key Technologies
* **Framework:** .NET 8.0, WinForms (Container)
* **Interop:** P/Invoke (User32, DwmApi, UxTheme, Kernel32)
* **UI Engine:** Microsoft WebView2 (Chromium-based)
* **Monitoring:** `SetWinEventHook` (`EVENT_SYSTEM_FOREGROUND`, `EVENT_OBJECT_NAMECHANGE`)

#### API Usage Example

```csharp
// Apply Mica backdrop
int backdrop = DWMSBT_MAINWINDOW;
DwmSetWindowAttribute(hwnd, DWMWA_SYSTEMBACKDROP_TYPE, ref backdrop, 4);

// Set title bar color (BGR format)
int color = (c.B << 16) | (c.G << 8) | c.R;
DwmSetWindowAttribute(hwnd, DWMWA_CAPTION_COLOR, ref color, 4);

// Hide native title bar text
var wta = new WTA_OPTIONS {
    dwFlags = WTNCA.NODRAWCAPTION | WTNCA.NODRAWICON,
    dwMask = WTNCA.NODRAWCAPTION | WTNCA.NODRAWICON
};
SetWindowThemeAttribute(hwnd, WTA_NONCLIENT, ref wta, sizeof(WTA_OPTIONS));
