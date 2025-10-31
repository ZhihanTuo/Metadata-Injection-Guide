# 360° and 3D Video Metadata Guide

This guide explains how to use `FFmpeg` to inject spatial metadata into video files. This metadata is essential for platforms like YouTube and VR video players (like VLC) to correctly recognize and display 360° and 3D content.

## 1. Prerequisites

You must have [FFmpeg](https://ffmpeg.org/download.html) installed and accessible from your command line. This guide also uses `ffprobe`, which is included in the `FFmpeg` download.

## 2. How to Inject Metadata

The basic command structure is:
`ffmpeg -i "input.mp4" -c copy [METADATA_TAGS] "output.mp4"`

(**PLEASE REPLACE `"input.mp4"` and `"output.mp4"` with the names of your original file and new file**)

The `-c copy` argument is crucial. It copies the video and audio streams without re-encoding, so it's extremely fast and loses no quality.

---

### 2.1. For 360° Monoscopic (Standard 360° Video)

* **Use Case:** Standard 360° videos
* **Command:**
    ```bash
    ffmpeg -i "input_360.mp4" -c copy -metadata:s:v:0 "spherical-projection=equirectangular" "output_360.mp4"
    ```

### 2.2. For 3D Side-by-Side (Flat Video)

* **Use Case:** 3D gameplay recordings from Meta Quest (using QGO). This is **not** spherical.
* **Command:**
    ```bash
    ffmpeg -i "quest_recording.mp4" -c copy -metadata:s:v:0 "stereo_mode=left_right" "output_quest_3D.mp4"
    ```

### 2.3. For 3D 180° (VR180)

* **Use Case:** VR180 camera footage (fisheye, side-by-side).
* **Command:** (Requires two tags)
    ```bash
    ffmpeg -i "input_vr180.mp4" -c copy -metadata:s:v:0 "spherical-projection=fisheye" -metadata:s:v:0 "stereo_mode=left_right" "output_vr180.mp4"
    ```

### 2.4. For 3D 360° (Stereoscopic Spherical)

* **Use Case:** Professional 3D 360° cameras (often in top-bottom format).
* **Command:** (Requires two tags)
    ```bash
    ffmpeg -i "input_3d_360.mp4" -c copy -metadata:s:v:0 "spherical-projection=equirectangular" -metadata:s:v:0 "stereo_mode=top_bottom" "output_3d_360.mp4"
    ```

## 3. How to Check Your Work

There are two primary ways to confirm the metadata was injected correctly.

### 3.1. Technical Check (Command Line)

Use `ffprobe` to read the metadata tags directly from the video stream.

**Command:**
```bash
ffprobe -v error -show_streams -select_streams v:0 -show_entries "stream_tags" "your_output_file.mp4"
```

### 3.2. Visual Check

The easiest way is to open your output file VLC Media Player. If the metadata was successfully attached, you should be able to look around with either your mouse or arrow keys. 