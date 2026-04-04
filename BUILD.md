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

### 5. Build

```bash
pyinstaller QualityScaler.spec
```

The bundled app will be output to `dist/QualityScaler/`.

### 6. Run the built app

```bash
./dist/QualityScaler/QualityScaler
```

## Notes

- The `AI-onnx/` models are bundled into the build at step 5 — make sure all desired model files are present before building.
- `build/` and `dist/` are gitignored and safe to delete between builds.
- To rebuild cleanly: `rm -rf build/ dist/` then re-run `pyinstaller QualityScaler.spec`.
