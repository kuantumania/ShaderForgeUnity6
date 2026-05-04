# Shader Forge — Unity 6 Fork

A visual, node-based shader editor for Unity (Built-in Render Pipeline).
This is a Unity 6 compatible fork of [Freya Holmér's Shader Forge](https://github.com/FreyaHolmer/ShaderForge).

---

## Installation (Unity Package Manager)

Open **Window → Package Manager**, click **+** → **Add package from git URL** and paste:

```
https://github.com/kuantumania/ShaderForgeUnity6.git?path=package
```

Or add it manually to your project's `Packages/manifest.json`:

```json
{
  "dependencies": {
    "com.kuantumania.shaderforge": "https://github.com/kuantumania/ShaderForgeUnity6.git?path=package"
  }
}
```

To import the example shaders and assets: **Package Manager → Shader Forge → Samples → Import**.

---

## Usage

After installing the package, open the editor from the menu:

**Tools → Shader Forge**

From there you can pick a shader template (Unlit, Lit/PBR, Lit/Basic, Custom Lighting, Sprite, Particle, Sky, Post-Effect) and start wiring nodes. The generated `.shader` asset will be saved into your `Assets/` folder.

For node reference, tutorials and general usage docs, the original Shader Forge wiki is still the best source:
[acegikmo.com/shaderforge/wiki](http://acegikmo.com/shaderforge/wiki/) — by Freya Holmér

---

## Render Pipeline Support

> **Built-in Render Pipeline only.** ShaderForge does **not** work with URP or HDRP.

This is a fundamental design limitation, not a Unity 6 issue — the original Shader Forge has never supported the Scriptable Render Pipelines either. The shaders it generates use Built-in RP-only constructs (`ForwardBase`/`ForwardAdd` passes, `UnityCG.cginc`, `_LightColor0`, etc.). In a URP/HDRP project, materials made with ShaderForge will render as magenta or fall back to unlit.

If your project uses URP or HDRP, use Unity's Shader Graph instead.

Built-in Render Pipeline is still fully supported in Unity 6 — it is just no longer the default. To use ShaderForge, create a project with the **Built-in** template (or set `GraphicsSettings → Default Render Pipeline` to `None`).

---

## Unity 6 Changes

The original Shader Forge was built for Unity 5/2019. The following breaking changes were fixed to make it compatible with Unity 6:

**C# API fixes**
- `RenderSettings.ambientLight` → `RenderSettings.ambientSkyColor` (deprecated in Unity 6)
- Removed dead `#if UNITY_5_0` preprocessor block (`PlayerSettings.useDirect3D11`)

**Generated shader fixes**
- `#pragma multi_compile LIGHTMAP_OFF LIGHTMAP_ON` → `#pragma multi_compile _ LIGHTMAP_ON`
- `#pragma multi_compile DIRLIGHTMAP_OFF DIRLIGHTMAP_COMBINED DIRLIGHTMAP_SEPARATE` → `#pragma multi_compile _ DIRLIGHTMAP_COMBINED` (`DIRLIGHTMAP_SEPARATE` removed in Unity 5.6)
- `#pragma multi_compile DYNAMICLIGHTMAP_OFF DYNAMICLIGHTMAP_ON` → `#pragma multi_compile _ DYNAMICLIGHTMAP_ON`
- `#pragma fragmentoption ARB_precision_hint_fastest` removed (causes errors in Unity 6)
- `SHOULD_SAMPLE_SH` macro updated to use `!defined(LIGHTMAP_ON)` instead of `defined(LIGHTMAP_OFF)`
- `#ifdef LIGHTMAP_OFF` → `#ifndef LIGHTMAP_ON`
- `_Object2World` → `unity_ObjectToWorld` in the World Position node (the legacy `_Object2World` matrix was removed back in Unity 5.4 and does not exist in Unity 6)

All existing `.shader` files (example shaders and preset shaders) were updated with the same fixes.

---

## UPM Package Compatibility

Beyond the Unity 6 fixes, the following changes were needed so ShaderForge can run when installed as a UPM package (the original was only meant to live under `Assets/`):

- **Internal resource path lookup** — `SF_Resources.SearchForInternalResourcesPath()` was hardcoded to scan `AssetDatabase` for `/ShaderForge/Editor/InternalResources/`. It now uses `PackageManager.PackageInfo.FindForAssembly()` to resolve the package's own asset path, with a fallback to the legacy scan for `Assets/` installs.
- **Preset shader file read** — `SF_Editor.NewShader()` built an absolute path with `Application.dataPath + presetPath.Substring(6)`, assuming the preset always lived under `Assets/` (6 chars). For UPM (`Packages/`, 8 chars) this produced paths like `Assetses/com.kuantumania...` and threw `DirectoryNotFoundException`. Now reads the project-relative path directly.
- **Package manifest meta files** — Added `Editor.meta` and `package.json.meta` so Unity does not warn `"has no meta file, but it's in an immutable folder"`.
- **Assembly Definition** — Added `ShaderForge.Editor.asmdef` (Editor-only platform) so the package compiles into its own assembly.
- **Repo restructure** — UPM-installable contents live under `package/`, so the install URL is `…?path=package`.

---

## Requirements

- Unity 6000.0 or newer
- Built-in Render Pipeline

---


https://tldrlegal.com/license/mit-license

Copyright 2019 Freya Holmér

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
