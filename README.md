# Tada

Turn what a photo knows about itself — depth, faces, bodies, colours, metadata — into images. Nothing is sent: every model runs on the device.

**Use:** open the page on your phone, choose a photo, pick a style, adjust, save. "Add to Home Screen" to keep it as an app.

Sister project of [Notus](https://github.com/kamyukami/notus): same screen, same gestures, same single static page.

## Gestures

- Drag vertically (photo or bottom panel): pick an option.
- Drag horizontally or turn the dial: adjust. Flick to spin the dial.
- Tap the photo: full screen (pinch to zoom). Long press: show the original.

## Data

Each layer is computed the first time something needs it, then kept until the next photo. The Data panel shows the layers over the photo (never exported).

| Layer | What it is | Model |
|---|---|---|
| Depth | 0 far … 1 near, plus normals derived from it | Depth Anything V2 small, int8 (35 MB) |
| Faces | boxes, 106 landmarks, outline | SCRFD-2.5G + 2d106det (InsightFace) |
| Bodies | up to 6 people × 17 keypoints | MoveNet MultiPose Lightning (19 MB) |
| Person | matte of the main subject | u2netp (4.5 MB) |
| Colours | k-means palette (4, 8, 16) | none |
| Metadata | camera, lens, exposure, date, GPS | none (EXIF read from the file) |

## Styles

Fog (depth → colour) · Relief (relit from the normals) · Shift (RGB split by depth) · Cutout (background → flat colour or the photo's dominant colour) · Wire (face outlines + skeletons) · Poster (k dominant colours) · Stamp (metadata typeset: caption, film date, frame) · Map (the raw depth / heat / normals / mask).

Exports JPEG or PNG, no EXIF.

## Technical

A single static page, no build step. Inference: [onnxruntime-web](https://github.com/microsoft/onnxruntime) 1.22, WebAssembly, one thread.

```
index.html   the app
vendor/      onnxruntime-web (MIT)
models/      ONNX models (see licences)
```

Serve the folder as is (GitHub Pages, or `python3 -m http.server`).

Depth runs at 518 × 518 single-threaded: expect a few seconds on a phone the first time a style needs it.

## Licences

- **Code**: MIT, see [LICENSE](LICENSE).
- **onnxruntime-web**: MIT, © Microsoft.
- **depth.onnx**: Depth Anything V2 small (Apache 2.0), ONNX export from [fabio-sim/Depth-Anything-ONNX](https://github.com/fabio-sim/Depth-Anything-ONNX), weights quantised to int8 (MatMul/Gemm).
- **movenet.onnx**: MoveNet MultiPose Lightning (Apache 2.0, Google), ONNX from [Kazuhito00/MoveNet-Python-Example](https://github.com/Kazuhito00/MoveNet-Python-Example).
- **u2netp.onnx**: U²-Net small (Apache 2.0), from [danielgatis/rembg](https://github.com/danielgatis/rembg).
- **det_2.5g.onnx, 2d106det.onnx**: released by [InsightFace](https://github.com/deepinsight/insightface) **for non-commercial research use only** — same caveat as Notus, to be replaced before any stable release.
