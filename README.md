# FFmpeg Shared Build for Windows (x86_64)

Pre-built FFmpeg shared libraries and executables for **Windows x86_64** only.

> **Platform:** Windows 10/11 (64-bit) only. These binaries are **not compatible** with Linux or macOS.

## Version

- **FFmpeg:** N-122740-g0a629df0a8 (built 2026-02-13)
- **Architecture:** x86_64 (64-bit)
- **Toolchain:** GCC 15.2.0 (crosstool-NG), cross-compiled with MinGW-w64
- **Linking:** Shared libraries (DLLs)
- **License:** GPL v3

## Contents

```
bin/
  ffmpeg.exe          - Media converter/encoder/decoder
  ffplay.exe          - Simple media player
  ffprobe.exe         - Media file analyzer
  avcodec-62.dll      - Codec library
  avdevice-62.dll     - Device I/O library
  avfilter-11.dll     - Filter library
  avformat-62.dll     - Format/muxer library
  avutil-60.dll       - Utility library
  swresample-6.dll    - Audio resampling library
  swscale-9.dll       - Image scaling/color conversion library
```

## Quick Start

1. Download or clone this repository
2. Add the `bin/` folder to your system PATH (see [INSTALLATION.md](INSTALLATION.md))
3. Open a terminal and verify:

```cmd
ffmpeg -version
ffprobe -version
```

## Common Usage Examples

### Get media file information
```cmd
ffprobe -v quiet -print_format json -show_format -show_streams input.mp4
```

### Convert video format
```cmd
ffmpeg -i input.avi -c:v libx264 -c:a aac output.mp4
```

### Extract audio from video
```cmd
ffmpeg -i input.mp4 -vn -c:a libmp3lame -q:a 2 output.mp3
```

### Record screen (Windows GDI)
```cmd
ffmpeg -f gdigrab -framerate 30 -i desktop -c:v libx264 output.mp4
```

### Convert to WebM (for web)
```cmd
ffmpeg -i input.mp4 -c:v libvpx-vp9 -c:a libopus output.webm
```

### Generate thumbnail from video
```cmd
ffmpeg -i input.mp4 -ss 00:00:05 -frames:v 1 thumbnail.jpg
```

### Concatenate multiple files
```cmd
ffmpeg -f concat -safe 0 -i filelist.txt -c copy output.mp4
```

### Stream to RTMP
```cmd
ffmpeg -re -i input.mp4 -c:v libx264 -c:a aac -f flv rtmp://server/live/stream_key
```

## Enabled Libraries

This build includes 70+ libraries:

| Category | Libraries |
|----------|-----------|
| **Video Codecs** | libx264, libx265, libvpx, libaom (AV1), libsvtav1, librav1e, libopenh264, libxvid, libxavs2, libdavs2, libuavs3d, libvvenc, libkvazaar, liboapv |
| **Audio Codecs** | libmp3lame, libopus, libvorbis, libopencore-amrnb/amrwb, libtwolame, libsoxr |
| **Image Formats** | libjxl (JPEG XL), libwebp, libopenjpeg |
| **Subtitles** | libass, libaribcaption, libaribb24, libzvbi |
| **Filters** | libvmaf, frei0r, libplacebo, libvidstab, libzimg, librubberband |
| **Hardware Accel** | CUDA (NVENC/NVDEC), AMF (AMD), VAAPI, Vulkan, OpenCL, Intel VPL |
| **Streaming** | libsrt, librist, libzmq, libssh |
| **AI/ML** | whisper (speech recognition) |
| **Other** | SDL2, OpenAL, chromaprint, libbluray, libgme, libopenmpt, lv2 |

## Use with Node.js (fluent-ffmpeg)

```javascript
const ffmpeg = require('fluent-ffmpeg');

// Point to the binaries in this repo
ffmpeg.setFfmpegPath('./bin/ffmpeg.exe');
ffmpeg.setFfprobePath('./bin/ffprobe.exe');

ffmpeg('input.mp4')
  .output('output.webm')
  .on('end', () => console.log('Done'))
  .run();
```

## Use with Python (subprocess)

```python
import subprocess

result = subprocess.run(
    ['./bin/ffprobe.exe', '-v', 'quiet', '-print_format', 'json',
     '-show_format', '-show_streams', 'input.mp4'],
    capture_output=True, text=True
)
print(result.stdout)
```

## Why Windows Only?

These are **PE (Portable Executable)** binaries and **DLL** shared libraries compiled specifically for the Windows x86_64 platform using MinGW-w64. They will not run on:

- **Linux** - Uses ELF binary format and `.so` shared libraries
- **macOS** - Uses Mach-O binary format and `.dylib` shared libraries

For other platforms, build FFmpeg from source or use platform-specific package managers:
- **Linux:** `sudo apt install ffmpeg` (Debian/Ubuntu) or `sudo dnf install ffmpeg` (Fedora)
- **macOS:** `brew install ffmpeg`

## License

FFmpeg is licensed under the **GNU General Public License v3 (GPL v3)**. See [LICENSE.md](LICENSE.md) for details.

This build includes GPL-licensed components (libx264, libx265, etc.), making the entire build GPL v3.