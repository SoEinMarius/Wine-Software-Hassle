# Genshin Impact Tutorial Slideshow Crash Fix (AAGL / Linux)

This guide documents the fix for an issue where **Genshin Impact crashes during 5-star character tutorials or trial domains** when using **An Anime Game Launcher (AAGL)** on Linux distributions (like CachyOS / Arch).

## Cause
The introductory slideshows in new character tutorials rely heavily on Windows **Media Foundation (`mfplat`)** components to render UI and web assets. If your custom Wine/Proton runner (such as `Spritz`) lacks these components, the assets fail to load. Once the in-game script times out waiting for the slideshow images, it triggers a hard desktop crash.

## Solution
Follow these steps to manually inject the required Media Foundation components and configure the DLL overrides for your specific game prefix.

### 1. Install Winetricks & Dependencies
Ensure `winetricks` and `cabextract` (needed to unpack the official cabinet files) are installed via your package manager:

```bash
# Arch Linux / CachyOS
sudo pacman -S winetricks cabextract
```

### 2. Extract Media Foundation Files
Run Winetricks directly against your AAGL Wine prefix to download and extract the base `mf` components. 

*Note: Replace the path below if your AAGL instance uses a custom installation directory.*

```bash
WINEPREFIX=$HOME/.local/share/anime-game-launcher/prefix/ winetricks mf
```
*(Ignore any warnings regarding a 64-bit WINEPREFIX or experimental wow64 mode).*

### 3. Configure Manual DLL Overrides
Modern versions of Winetricks include `mfplat` inside the `mf` verb, but you must explicitly force Wine to prioritize the newly native Windows libraries over its own built-in stubs.

1. Open the Wine configuration manager for the game prefix:
   ```bash
   WINEPREFIX=$HOME/.local/share/anime-game-launcher/prefix/ winecfg
   ```
2. Navigate to the **Libraries** (Bibliotheken) tab.
3. In the **"New override for library"** input field, type **`mfp`** and click **Add**.
4. Repeat the same step for **`mfplat`** (Enhanced Video Renderer) and click **Add**.
5. Repeat the same step for **`evr`** (Enhanced Video Renderer) and click **Add**.
6. Verify that **`mf`**, **`mfplat`**, and **`evr`** are all present in the existing overrides list and explicitly set to **(native, builtin)**.
7. Click **Apply** and **OK**.

### 4. Clear Game UI Cache
To ensure no corrupted placeholder images are stuck in the cache:
1. Open AAGL and launch the game.
2. On the main title screen (before entering the game world), click the **Wrench / Game Files Integrity Check** icon on the right menu.
3. Log into the game and enter the tutorial domain safely.
