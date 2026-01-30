# WOW Hand Gesture Recognition System

A lightweight hand-gesture recognition demo that detects a "WOW" gesture using MediaPipe hand landmarks and simple geometric heuristics. The project contains a runnable demo and a Jupyter notebook demonstrating the algorithm and visualization.

## Features

- Real-time hand detection using MediaPipe.
- Extracts 21 hand landmarks and derives a simple geometry image.
- Detects a circular gesture formed by thumb and index using scaled distance thresholds.
- Uses Hough Line Transform on the derived geometry to confirm the gesture shape.
- Displays live overlays and geometry visualization.

## Repository Structure

- `demo.py` — (root) simple runner (committed) to start the demo from the command line.
- `notebooks/wow_gesture.ipynb` — interactive Jupyter notebook with the code used to develop and visualize the algorithm.
- `.gitignore` — ignores typical virtualenv and OS artifacts.

## Algorithm Overview

1. Capture frames from webcam using OpenCV.
2. Run MediaPipe Hands to detect a single hand and extract normalized landmarks.
3. Convert normalized landmarks to pixel coordinates using frame width/height.
4. Build a binary `geometry` canvas by drawing lines between selected landmark pairs (finger segments).
5. Compute the distance between thumb tip (`landmark 4`) and index tip (`landmark 8`) and a reference `hand_size` (distance between wrist/landmark 0 and a middle-hand landmark 9).
6. Use ratio thresholds (`CIRCLE_ENTER_RATIO`, `CIRCLE_EXIT_RATIO`) to detect when thumb-index form a small/closed configuration and maintain stability over a few frames (`CIRCLE_STABLE_FRAMES`).
7. When a stable circle-like hold is detected, draw a circle on the geometry canvas and apply `cv2.HoughLinesP` to count lines inside the geometry.
8. The gesture is considered a "WOW" when both the circle hold is active and `line_count >= 2`.

This approach is intentionally simple and fast: it relies on geometric heuristics rather than training a model, which makes it easy to run in real time on CPU.

## Requirements

- Python 3.8+
- OpenCV
- MediaPipe
- NumPy

Install dependencies with pip:

```bash
pip install opencv-python mediapipe numpy
```

If you use a virtual environment:

```bash
python -m venv .venv
# Windows
.venv\Scripts\activate
# macOS/Linux
source .venv/bin/activate
pip install -r requirements.txt
```

(You can optionally create a `requirements.txt` with the above packages.)

## Usage — Run Demo

From the project root, run:

```bash
python demo.py
```

Or open and run the notebook `notebooks/wow_gesture.ipynb` in JupyterLab/Jupyter Notebook:

```bash
jupyter notebook notebooks/wow_gesture.ipynb
```

The demo opens two windows:

- `WOW Gesture Recognition` — the camera feed with landmark overlays and status text.
- `Geometry` — the binary geometry image used for Hough Line detection.

Press `Esc` to exit.

## Notes & Tuning

- Thresholds in the notebook are:
  - `CIRCLE_ENTER_RATIO = 0.25`
  - `CIRCLE_EXIT_RATIO  = 0.32`
  - `CIRCLE_STABLE_FRAMES = 5`
- These constants are tuned heuristically and may need adjustment for different camera distances, resolutions, or hand sizes.
- `cv2.HoughLinesP` params (`threshold`, `minLineLength`, `maxLineGap`) influence line detection sensitivity.

## Troubleshooting

- If the camera does not open, ensure no other app is using it and that the correct device index is used (`cv2.VideoCapture(0)` might need to be changed).
- If MediaPipe cannot be imported, confirm installation: `pip show mediapipe`.
- On Windows with OneDrive, avoid running from a syncing folder for low-latency webcam performance (consider moving to a local folder).

## Contributing

Contributions are welcome. Suggested improvements:

- Replace heuristics with a light classifier for more robust gesture detection.
- Add configurable CLI flags for camera index and threshold tuning.
- Add unit tests for geometry generation methods.

## License

This repository does not include a license file. Add a license (e.g., MIT) if you want others to reuse the code.

## Contact

For questions, reach out to the project owner.
