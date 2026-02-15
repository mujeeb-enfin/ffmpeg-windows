# Installation Guide (Windows)

## Prerequisites

- Windows 10 or 11 (64-bit)
- No additional dependencies required — all DLLs are included

## Method 1: Add to System PATH (Recommended)

This makes `ffmpeg`, `ffprobe`, and `ffplay` available from any terminal.

### Using GUI

1. Clone or download this repository to a permanent location, e.g. `C:\ffmpeg`
2. Press `Win + R`, type `sysdm.cpl`, press Enter
3. Go to **Advanced** tab → **Environment Variables**
4. Under **System variables**, select **Path** → **Edit**
5. Click **New** and add the full path to the `bin` folder, e.g.:
   ```
   C:\ffmpeg\bin
   ```
6. Click **OK** on all dialogs
7. Open a **new** terminal and verify:
   ```cmd
   ffmpeg -version
   ```

### Using PowerShell (Admin)

```powershell
# Add to system PATH permanently (run as Administrator)
$ffmpegPath = "C:\ffmpeg\bin"
$currentPath = [Environment]::GetEnvironmentVariable("Path", "Machine")
if ($currentPath -notlike "*$ffmpegPath*") {
    [Environment]::SetEnvironmentVariable("Path", "$currentPath;$ffmpegPath", "Machine")
    Write-Host "Added $ffmpegPath to system PATH. Restart your terminal."
} else {
    Write-Host "Already in PATH."
}
```

### Using Command Prompt (Admin)

```cmd
setx /M PATH "%PATH%;C:\ffmpeg\bin"
```

## Method 2: Use Directly Without PATH

Reference the full path to the executables:

```cmd
C:\ffmpeg\bin\ffmpeg.exe -i input.mp4 output.avi
C:\ffmpeg\bin\ffprobe.exe -v quiet -print_format json -show_streams input.mp4
```

## Method 3: Use in a Node.js Project

Copy the `bin/` folder into your project and reference it:

```javascript
const ffmpeg = require('fluent-ffmpeg');
const path = require('path');

ffmpeg.setFfmpegPath(path.join(__dirname, 'bin', 'ffmpeg.exe'));
ffmpeg.setFfprobePath(path.join(__dirname, 'bin', 'ffprobe.exe'));
```

Or install the path helper:

```bash
npm install ffmpeg-static ffprobe-static
```

## Verifying the Installation

After installation, run these commands to verify:

```cmd
:: Check version
ffmpeg -version

:: Check available encoders
ffmpeg -encoders

:: Check available decoders
ffmpeg -decoders

:: Check hardware acceleration
ffmpeg -hwaccels

:: Analyze a media file
ffprobe -v quiet -print_format json -show_format -show_streams yourfile.mp4
```

## Hardware Acceleration

This build supports hardware-accelerated encoding/decoding:

### NVIDIA GPU (NVENC/NVDEC)

Requires NVIDIA GPU with updated drivers (Game Ready or Studio drivers).

```cmd
:: H.264 encoding with NVIDIA GPU
ffmpeg -i input.mp4 -c:v h264_nvenc -preset p4 -cq 23 output.mp4

:: H.265/HEVC encoding with NVIDIA GPU
ffmpeg -i input.mp4 -c:v hevc_nvenc -preset p4 -cq 28 output.mp4

:: Hardware-accelerated decoding + encoding
ffmpeg -hwaccel cuda -i input.mp4 -c:v h264_nvenc output.mp4
```

### AMD GPU (AMF)

Requires AMD GPU with Adrenalin drivers.

```cmd
:: H.264 encoding with AMD GPU
ffmpeg -i input.mp4 -c:v h264_amf output.mp4

:: H.265/HEVC encoding with AMD GPU
ffmpeg -i input.mp4 -c:v hevc_amf output.mp4
```

### Intel GPU (QSV via VPL)

Requires Intel GPU with Intel Media SDK/oneVPL.

```cmd
:: H.264 encoding with Intel GPU
ffmpeg -i input.mp4 -c:v h264_qsv output.mp4
```

## Troubleshooting

### "ffmpeg is not recognized as an internal or external command"
- Make sure the `bin/` folder path is added to your system PATH
- Open a **new** terminal window after changing PATH
- Verify the path exists: `dir C:\ffmpeg\bin\ffmpeg.exe`

### "The code execution cannot proceed because avcodec-62.dll was not found"
- All DLL files must be in the **same folder** as the `.exe` files
- Do not move `.exe` files without also moving the `.dll` files

### "This app can't run on your PC"
- You are running a 32-bit version of Windows — these binaries require 64-bit Windows

### NVENC errors
- Update your NVIDIA GPU drivers to the latest version
- Ensure your GPU supports NVENC (GTX 600+ / Quadro K series+)
