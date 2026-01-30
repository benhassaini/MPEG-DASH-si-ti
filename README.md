# 🎥 Video Processing and MPEG-DASH Experimental Pipeline

This repository documents the main processing steps used to generate multi-representation video content, compute objective quality metrics, and prepare MPEG-DASH streams for experimental evaluation.

The commands shown below are indicative and describe the general methodology without exposing the full set of experimental parameters.

---

## 📌 Test Sequences

- Big Buck Bunny (Blender Foundation)  
- Sintel (Blender Foundation).

Source example:  
https://download.blender.org/demo/movies/BBB/

---

## 🛠 Tools

- FFmpeg  
- MP4Box (GPAC)  
- siti-tools

---

## 🧪 Processing Pipeline

### 1. Multi-resolution and Multi-bitrate Transcoding

ffmpeg -i input_video.mp4 -s <resolution> -c:v libx264 -b:v <bitrate> -g <GOP> -an output_video.mp4

A reference Full-HD video is transcoded into multiple spatial resolutions and bitrates to generate different representations.

---

### 2. Encoder Configuration for Objective Quality Metrics (PSNR)

ffmpeg -i input_video.mp4 -c:v libx264 -tune psnr output_video.mp4

Psychovisual optimizations are disabled to ensure consistent objective quality measurements.

---

### 3. Objective Quality Assessment (PSNR / SSIM / VMAF)

ffmpeg -i distorted.mp4 -i reference.mp4 -lavfi libvmaf=ssim=1 -f null -

Each encoded representation is compared with the reference video to compute objective quality metrics.

---

### 4. Scene Change Detection

ffmpeg -i input_video.mp4 -filter:v "select='gt(scene,THRESHOLD)'" -f null -

Scene boundaries are detected for content-aware analysis.

---

### 5. GOP and Keyframe Alignment

ffmpeg -i input_video.mp4 -g <GOP> -keyint_min <GOP> -sc_threshold 0 output_keyframe.mp4

Keyframes are aligned across representations to guarantee segment synchronization for adaptive streaming.

---

### 6. Video Segmentation (Fixed Duration)

MP4Box -split <segment_duration> -out output_segments input_video.mp4

Encoded videos are segmented into fixed-duration chunks (e.g., 5 seconds).

---

### 7. Segment Concatenation

ffmpeg -f concat -safe 0 -i file_list.txt -c copy merged.mp4

Segments are concatenated when reconstructing sequences for analysis or subjective evaluation.

---

### 8. MPEG-DASH Packaging

MP4Box -dash <segment_duration> -rap -frag-rap -out manifest.mpd input_video.mp4

Segments and MPD manifests compliant with MPEG-DASH are generated.

---

### 9. Spatial and Temporal Information Extraction (SI/TI)

siti-tools input_video.mp4 > output.json

Spatial (SI) and temporal (TI) complexity indicators are computed for content characterization.

---

## 📝 Notes

- Tools used: FFmpeg, MP4Box (GPAC), siti-tools  
- Test contents: Big Buck Bunny and Sintel  
- Fixed segment duration (e.g., 5 s) for MPEG-DASH compliance  
- Commands are simplified and provided for conceptual reproducibility  
- This repository focuses on methodology rather than exact experimental parameters

---

## 📄 License and Data

The original video contents (Big Buck Bunny, Sintel) are provided by the Blender Foundation under open licenses.  
Users must respect the original content licenses when redistributing data.
The datasets are available on: https://drive.google.com/drive/folders/1ebq3Z-35rfYNmyOAMNXMvTE9xcPE-8m8?usp=sharing
