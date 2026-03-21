# EEG LSL Viewer User Manual

## Overview
The **EEG LSL Viewer** is a real-time visualization tool for EEG data. It supports streaming from LSL (Lab Streaming Layer), direct serial port connection to FreeEEG32 hardware, and CSV playback. The application provides dynamic channel mapping through YAML montage files and features integrated signal processing filters.

---

## 1. Getting Started

### Prerequisites
- Python 3.8+
- Required libraries: `brainflow`, `PyQt5`, `pyqtgraph`, `numpy`, `scipy`, `PyYAML`

### Installation
Install dependencies via pip:
```powershell
pip install brainflow PyQt5 pyqtgraph numpy scipy PyYAML
```

---

## 2. Running the Application

There are two main entry points for the viewer:

### A. Standard Viewer (Legacy/Fixed Config)
Uses `src/modules/config_channels.py` for channel mapping.
```powershell
python src/eeg_viewer_main.py [options]
```

### B. YAML-Based Viewer (Recommended)
Uses YAML montage files (e.g., `freg9.yaml`) for flexible channel mapping and virtual channel calculations.
```powershell
python src/eeg_viewer_main_yaml.py --config src/modules/freg9.yaml [options]
```

### Command Line Options
| Option | Description | Example |
| :--- | :--- | :--- |
| `--lsl-stream` | Connect to a specific LSL stream name or ID. | `--lsl-stream BrainFlowEEG` |
| `--serial-port` | Connect to hardware via COM port. | `--serial-port COM3` |
| `--board-id` | BrainFlow board ID (Default: 17 for FreeEEG32). | `--board-id 17` |
| `--playback-file`| Play back a recorded CSV file. | `--playback-file data.csv` |
| `--config` | (YAML version only) Path to the montage YAML. | `--config src/modules/freg9.yaml` |

---

## 3. User Interface Guide

### Visualization Panels
1.  **Time-Series Plot (Left)**: Displays raw or filtered EEG signals in real-time. Each channel includes metrics:
    *   **PTP**: Peak-to-Peak amplitude (µV).
    *   **RMS**: Root Mean Square power.
    *   **DC**: DC offset.
    *   **Status**: Indicates if the signal is "OK", "FLAT", "NOISY", or "SPIKY".
2.  **Power Spectral Density (PSD) (Right Top)**: Shows the frequency distribution (1–300 Hz) in log scale.
3.  **Band Power (Right Bottom)**: Bar chart showing relative power in standard EEG bands: Delta (δ), Theta (θ), Alpha (α), Beta (β), and Gamma (γ).

### Control Panel
-   **Y Range**: Manually set the µV range or enable "Auto" scaling.
-   **PSD Y Range**: Adjust the vertical scale for the PSD plot.
-   **Channels ON/OFF**: Toggle individual channels to focus on specific signals.

---

## 4. Signal Processing & Filters

The application includes a real-time filter engine accessible via checkboxes:
-   **Detrend**: Removes linear trends from the signal (highly recommended).
-   **RTHP 1Hz**: Real-time high-pass filter to remove slow drifts.
-   **Bandpass (3–333 Hz)**: Zero-phase Butterworth filter for a clean frequency window.
-   **Notch 50+H Hz**: Removes 50Hz line noise and its harmonics (100, 150, 200, 250 Hz).
-   **Notch rt 50 Hz**: Real-time recursive notch filter for lower latency.

---

## 5. Configuration & Montages

### YAML Montage (freg9.yaml)
The YAML system allows you to define:
-   **Physical Channels**: Mapping hardware inputs to 10-20 names.
-   **Virtual Channels**: Creating new signals via weighted sums (e.g., Weighted Laplacians for S1 focus).
-   **Pick Channels**: Select which channels to display in the UI.

Example Virtual Channel Definition:
```yaml
virtual_channels:
  S1_left:
    base: C3
    weights: {FC3: 2, CP3: 2, Cz: 1, T7: 1}
    divisor: 6
```

---

## 6. Troubleshooting
-   **No data appearing**: Ensure the LSL stream is active or the correct COM port is selected.
-   **"FLAT" Status**: Check electrode connectivity or impedance.
-   **"NOISY" Status**: Ensure the ground electrode is properly attached and check for nearby electronics interference.
