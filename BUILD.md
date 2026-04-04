# Building QualityScaler

## Prerequisites

- Python 3.12
- `git`
- `ffmpeg` installed and on your PATH
- `exiftool` installed and on your PATH

## Steps

### 1. Clone the repo

```bash
git clone https://github.com/moontato/QualityScaler-Linux.git
cd QualityScaler-Linux
```

### 2. Create and activate a virtual environment

```bash
python3 -m venv .venv
source .venv/bin/activate
```

### 3. Install dependencies

```bash
pip install -r requirements.txt
```

### 4. Download the AI models

Download from https://gofile.io/d/b4Ds9u and extract all `.onnx` files into the `AI-onnx/` directory.

The expected files are:

```
AI-onnx/
  BSRGANx2_fp16.onnx
  BSRGANx4_fp16.onnx
  IRCNN_Lx1_fp16.onnx
  IRCNN_Mx1_fp16.onnx
  LVAx2_fp16.onnx
  MSharpx4_fp16.onnx
  RealESR_Ax4_fp16.onnx
  RealESR_Gx4_fp16.onnx
  RealESRGANx4_fp16.onnx
```

### 5. Generate the .spec file

The `.spec` file is gitignored and must be generated before building. Run:

```bash
pyi-makespec \
  --name QualityScaler \
  --noconsole \
  --icon Assets/logo.png \
  --add-data "Assets:Assets" \
  --add-data "AI-onnx:AI-onnx" \
  --hidden-import customtkinter \
  --hidden-import PIL \
  --hidden-import PIL._tkinter_finder \
  --hidden-import cv2 \
  --hidden-import onnxruntime \
  --hidden-import psutil \
  --hidden-import natsort \
  QualityScaler.py
```

`PIL._tkinter_finder` must be explicitly included — PyInstaller does not auto-detect it, and the app will crash on launch without it.

### 6. Build

```bash
pyinstaller QualityScaler.spec
```

The bundled app will be output to `dist/QualityScaler/`.

### 7. Run the built app

```bash
./dist/QualityScaler/QualityScaler
```

## Notes

- The `AI-onnx/` models are bundled into the build at step 6 — make sure all desired model files are present before building.
- `build/` and `dist/` are gitignored and safe to delete between builds.
- To rebuild cleanly: `rm -rf build/ dist/` then re-run `pyinstaller QualityScaler.spec`.

## GPU Acceleration (CUDA)

The app uses `onnxruntime-gpu` and will automatically use CUDA if available, otherwise falling back to CPU.

`onnxruntime-gpu` requires **CUDA 12** and **cuDNN 9**. If your system has a different CUDA version, check whether the installed version of `onnxruntime-gpu` supports it before installing cuDNN:

```bash
pip index versions onnxruntime-gpu
```

Installing `libcudnn9-cuda-12` alongside a newer CUDA version (e.g. CUDA 13) risks library path conflicts. Safer alternatives:
- Use a conda environment or NVIDIA NGC container that ships CUDA 12 + cuDNN 9 pre-configured, leaving your system CUDA install untouched.
- Or run without GPU acceleration — the app falls back to CPU cleanly with no other issues.
