# GMM Object Tracking

> Gaussian Mixture Model (GMM) based pipeline to detect, segment, and track moving objects in video streams or image sequences.

## Overview

This repository demonstrates a practical implementation of background modeling and object tracking using a **Gaussian Mixture Model (GMM)** for background subtraction and simple tracking logic across frames. The project is intended for educational use and as a starting point for more advanced multi-object tracking systems.

Use cases:

* Surveillance and anomaly detection prototypes
* Traffic & pedestrian motion analysis (small-scale)
* Robotics perception demos

## Features

* Per-frame background modeling using GMM (adaptive background subtraction)
* Foreground mask post-processing (morphological cleaning, contour filtering)
* Simple object localization (bounding boxes + centroids)
* Basic tracking by matching detections between frames (IoU / centroid distance)
* Visualization of tracks and bounding boxes on video output
* Notebook and script examples for quick experimentation

## Getting Started

### Requirements

* Python 3.8+ recommended
* Common packages: `numpy`, `opencv-python`, `scikit-learn`, `scipy`, `matplotlib`, `tqdm`, `pandas`

You can install the typical requirements with:

```bash
pip install -r requirements.txt
```

> If you don't have a `requirements.txt`, install individually:
>
> ```bash
> pip install numpy opencv-python scikit-learn scipy matplotlib tqdm pandas
> ```

### Installation

1. Clone the repository:

```bash
git clone <your-repo-url>
cd <your-repo>
```

2. (Optional) Create a virtual environment and activate it:

```bash
python -m venv venv
source venv/bin/activate   # Linux / macOS
venv\Scripts\activate    # Windows
```

3. Install dependencies (see previous section).

## Usage

### Run the notebook

A ready-to-run Jupyter notebook `GMM_to_track_movement_of_objects.ipynb` is included. Open it and run cells sequentially to experiment with video files and parameters.

```bash
jupyter notebook GMM_to_track_movement_of_objects.ipynb
```

The notebook contains sections for:

* Loading video / image sequence
* Training/adapting the GMM background model
* Generating foreground masks and extracting detections
* Simple data association across frames for tracking
* Visualization and saving output video

### Run as script

A script `run_tracking.py` (if included) can be run from the command line:

```bash
python run_tracking.py --input videos/sample.mp4 --output output/result.mp4 --min-area 500
```

Common CLI flags (may vary by script implementation):

* `--input` : input video path or camera index
* `--output` : output video path to save annotated result
* `--min-area` : ignore small detections under this pixel area
* `--display` : show annotated frames in a window while processing

## How it works (high level)

1. **Background modeling:** Use a GMM per pixel (or `cv2.createBackgroundSubtractorMOG2`) to estimate the background distribution and classify pixels as foreground when they deviate.
2. **Mask post-processing:** Apply morphological opening/closing, median blur, and small-object removal to reduce noise.
3. **Detection:** Find contours in the cleaned mask and compute bounding boxes and centroids for each object candidate.
4. **Tracking (simple):** For each new frame, match detections to existing tracks using a cost metric (e.g., centroid distance or IoU). If no match is found, start a new track. If a track is unmatched for `N` frames, mark it as lost.
5. **Visualization:** Draw bounding boxes, track IDs, and historical trajectories on frames and optionally save the video.

## Input / Output

* **Input:** Video file(s) (MP4, AVI) or image sequences. If using a live camera, pass camera index `0` or `1`.
* **Output:** Annotated video showing bounding boxes and track IDs, CSV/log file with per-frame detection coordinates (optional).

Example CSV columns:

```
frame,track_id,x,y,w,h,confidence
```

## Project structure

```
├── GMM_to_track_movement_of_objects.ipynb   # Main notebook
├── run_tracking.py                         # Optional script wrapper (if provided)
├── requirements.txt                        # Python dependencies
├── data/                                   # Example videos or sequences (not included)
├── output/                                 # Saved outputs (videos, CSVs)
├── src/
│   ├── background.py                       # GMM/background model helpers
│   ├── detection.py                        # Foreground mask -> contours -> bboxes
│   ├── tracker.py                          # Simple tracker (matching & track management)
│   └── utils.py                            # Visualization and I/O helpers
├── README.md
└── LICENSE
```

(Adjust structure if your repository layout differs.)

## Parameters you can tune

* GMM parameters: number of mixtures, learning rate, variance thresholds (or `history`, `varThreshold`, `detectShadows` for `MOG2`).
* Morphology: kernel size for opening/closing and number of iterations.
* Detection: `min-area` to filter small contours, aspect ratio filters.
* Tracking: distance threshold or IoU threshold for matching, maximum allowed missed frames before deleting a track.

## Evaluation & Visualization

* Visual inspection of annotated videos is the fastest way to validate results.
* Quantitative metrics (if ground truth is available):

  * **MOTA / MOTP** (for full multi-object benchmarks)
  * **ID switches**, **False Positives**, **False Negatives**, **Track fragmentation**
  * Simple object-level precision/recall if per-frame labels are available

Include sample output screenshots or short GIFs in the `output/` folder for the README to showcase results.

## Troubleshooting

* **No foreground detected:** Lower the GMM sensitivity (reduce `varThreshold`) or allow longer warm-up (`history`). Ensure video has motion and camera is not static for the entire sequence.
* **Too much noise:** Increase morphology kernel size, increase `min-area` threshold.
* **Tracks swapping IDs often:** Improve matching by combining IoU with motion prediction (e.g., simple linear Kalman filter) or use a proper tracker like SORT/DeepSORT.

## Contributing

Contributions are welcome! Suggested ways to contribute:

* Add unit tests and CI
* Plug in more advanced trackers (Kalman filter + Hungarian assignment, SORT, DeepSORT)
* Add evaluation scripts for MOT metrics
* Improve visualization and add configurable CLI

Please open issues or pull requests and describe changes clearly.


*Created with ❤️ — update the sections marked (optional) to better match your repository.*
