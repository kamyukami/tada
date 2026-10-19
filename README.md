# Tada

Turn what a photo knows about itself — depth, faces, bodies, colours, metadata — into images. Nothing is sent: every model runs on the device.

**Use:** open the page on your phone, choose a photo, pick a style, adjust, save. "Add to Home Screen" to keep it as an app.

Sister project of [Notus](https://github.com/kamyukami/notus): same screen, same gestures, same single static page.

## How it works

- **Transform** (rewrites the pixels) · **Add** (draws on top) · **Export**: each opens a drawer; pick an effect.
- The drawer closes and the bar becomes **Cancel / Done**. Drag vertically to pick an option, horizontally to set it (0…100, or −100…100). Double tap the value: default. Reset (top): all defaults.
- **Done** stacks the effect as a layer. **Layers**: reorder (↑ ↓), blend mode (press and slide through the list, or tap for the next), opacity (drag sideways), delete (×). Effects stack in order, without masks; every analysis always runs on the original photo. Undo / redo / Reset at the top.
- Tap the photo: full screen (pinch to zoom). Long press: the original.

## Analyses

Computed the first time an effect needs them, kept until the next photo.

| Analysis | Model |
|---|---|
| Depth (0 far … 1 near, normals derived) | Depth Anything V2 small, int8 (35 MB) |
| Faces (boxes, 106 landmarks, outline) | SCRFD-2.5G + 2d106det (InsightFace) |
| Bodies (6 people × 17 keypoints) | MoveNet MultiPose Lightning (19 MB) |
| Subject (matte) | u2netp (4.5 MB) |
| Colours (k-means palette) | — |
| Metadata (EXIF: camera, lens, exposure, date, GPS) | — |

## Effects

- Transform: Fog, Lens, Relief, Shift, Gradient, Cutout, Poster, Depth map, Normal map, Matte.
- Add: Face outline, Face points, Face boxes, Skeleton, Body boxes, Silhouette, Palette, Caption, Date stamp, Frame.

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
