# Technical Documentation: EEG Configuration Comparison

This document compares the channel mappings and electrode configurations between the **EEGlslviewer** (live visualization) and the **EEGsuite** (analysis pipeline) for the FreeEEG32 hardware.

---

## 1. Configuration Sources

*   **Viewer Config:** `src/modules/config_channels.py` (EEGlslviewer)
    *   *Date Reference:* 13/11 configuration.
    *   *Purpose:* Real-time LSL stream mapping and display.
*   **Analysis Config:** `src/modules/freg8.yaml` (EEGsuite / EEGlslviewer)
    *   *Name:* FREG8
    *   *Purpose:* Standardized 8-channel 10-20 montage for signal processing and analysis.

---

## 2. Channel Mapping Comparison

The following table compares the hardware input index (CH) to the assigned 10-20 electrode location for both configurations.

| Hardware CH | Electrode (Viewer) | Electrode (Analysis) | Status |
| :--- | :--- | :--- | :--- |
| **CH 1** | **T7** | **T7** | **Identical** |
| **CH 2** | **C3** | **C3** | **Identical** |
| **CH 5** | **T8** | **T8** | **Identical** |
| **CH 6** | **FC4** | **FC4** | **Identical** |
| **CH 9** | **FC3** | **FC3** | **Identical** |
| **CH 10** | **C4** | **C4** | **Identical** |
| **CH 13** | **CP3** | **CP3** | **Identical** |
| **CH 14** | **CP4** | **CP4** | **Identical** |
| **CH 26** | **x26** | NC (Not Connected) | Difference |
| **CH 30** | **x30** | NC (Not Connected) | Difference |

---

## 3. Detailed Similarities

### 10-20 Electrode Alignment
Both configurations are perfectly aligned for the primary 8 EEG channels. This ensures that data recorded or viewed in the `EEGlslviewer` will be correctly processed by the `EEGsuite` without needing to re-map indices.

### Logical Grouping
The configurations both target a specific 8-channel subset of the FreeEEG32's 32-channel capacity, focusing on the Central (C), Fronto-Central (FC), Temporal (T), and Centro-Parietal (CP) regions.

---

## 4. Detailed Differences

### Diagnostic / Test Channels
*   **EEGlslviewer:** Actively monitors **CH26** and **CH30**. 
    *   *CH26 (x26):* Annotated as "shorted with jumper" (used for noise floor testing).
    *   *CH30 (x30):* Annotated as "open | floating | no wire" (used for DC offset/interference testing).
*   **EEGsuite:** Ignores these channels (marked as `NC`), focusing purely on the 8 EEG signal electrodes.

### Format and Structure
*   **Python (Viewer):** Uses a dictionary `CHANNEL_MAPPING` to define active indices and an `EXG_CHANNELS` list to filter them. This is dynamic and allows skipping large gaps in channel numbers.
*   **YAML (Analysis):** Uses a fixed-length list (32 entries) where the position in the list defines the hardware channel. This is optimized for compatibility with MNE-Python and standard montage formats.

### Reference and Bias Notes
*   **Viewer:** Specifically notes "linked ears" as the reference and "GND (FPZ|FZ)" as the VCM_BIAS for the current 13/11 session.
*   **Analysis:** Now explicitly defines "linked ears" as the `reference` and "GND, between Fpz and Fz" as the `VCM_Bias`, ensuring full alignment with the acquisition setup.
*   **Montage:** Both use "standard_1020" for coordinate mapping.

---

## 5. Conclusion
For standard EEG data collection, the configurations are **functionally equivalent**. The only divergence is the inclusion of diagnostic test leads in the Viewer, which are excluded from the final analysis montage.
