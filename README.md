# Person From Vid

[![PyPI version](https://badge.fury.io/py/personfromvid.svg)](https://badge.fury.io/py/personfromvid) [![Python versions](https://img.shields.io/pypi/pyversions/personfromvid.svg)](https://pypi.org/project/personfromvid) [![License: GPL-3.0-or-later](https://img.shields.io/pypi/l/personfromvid.svg)](https://github.com/personfromvid/personfromvid/blob/main/LICENSE)

AI-powered video frame extraction and pose categorization tool designed for creating high-quality training datasets. Analyzes video files to identify and extract consistent, well-posed frames containing people - perfect for LoRA training, face datasets, and machine learning applications.

## Features

### 🎯 Dataset Creation Focused
- 📐 **Consistent Crop Formats**: Generate uniform square (1:1), portrait (4:3), or widescreen (16:9) crops perfect for ML training
- ✨ **AI-Powered Face Restoration**: GFPGAN enhancement automatically improves face quality in your dataset
- 🔄 **Resumable Batch Processing**: Process hundreds of videos reliably - interruptions automatically resume where they left off
- 📊 **Quality-Filtered Output**: Only saves high-quality, well-posed frames using advanced blur, brightness, and contrast metrics

### 🤖 Advanced AI Analysis
- 🎥 **Multi-Format Video Support**: Works with MP4, AVI, MOV, MKV, WebM, and more
- 🧠 **Intelligent Detection**: Uses state-of-the-art models for face detection (`yolov8s-face`), pose estimation (`yolov8s-pose`), and head pose analysis (`sixdrepnet`)
- 📐 **Pose & Shot Classification**: Automatically categorizes poses (standing, sitting, squatting) and shot types (closeup, medium shot, full body)
- 👤 **Head Orientation Analysis**: Classifies head directions into 9 cardinal orientations (front, profile, looking up/down, etc.)

### ⚡ Performance & Workflow
- 🚀 **GPU Acceleration**: Optional CUDA/MPS support for significantly faster processing of large datasets
- 🧠 **Smart Frame Selection**: Keyframe detection, temporal sampling, and deduplication ensure diverse, high-quality results
- 📊 **Rich Progress Tracking**: Modern console interface with real-time progress displays
- ⚙️ **Highly Configurable**: Extensive configuration options via CLI, YAML files, or environment variables

## Installation

### Prerequisites

- Python 3.10 or higher
- FFmpeg (for video processing)

#### Installing FFmpeg

**macOS:**
```bash
brew install ffmpeg
```

**Ubuntu/Debian:**
```bash
sudo apt update
sudo apt install ffmpeg
```

**Windows:**
Download from [FFmpeg official website](https://ffmpeg.org/download.html) or use:
```bash
choco install ffmpeg  # Using Chocolatey
```

### Install Person From Vid

#### From PyPI
The recommended way to install is via `pip`:
```bash
pip install personfromvid
```

#### From Source
Alternatively, to install from source:
```bash
git clone https://github.com/personfromvid/personfromvid.git
cd personfromvid
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate
pip install -e .
```

## Quick Start

```bash
# Standard square crops
personfromvid video.mp4 --crop-ratio 1:1 --face-restoration

# Specific resolution
personfromvid video.mp4 --crop-ratio 1:1 --face-restoration --resize 512 --output-dir ./dataset

# Portrait-oriented crops with enhanced faces
personfromvid video.mp4 --crop-ratio 4:3 --face-restoration --resize 768

# Batch processing multiple videos to the same dataset directory
personfromvid video1.mp4 --crop-ratio 1:1 --face-restoration --output-dir ./my_dataset
personfromvid video2.mp4 --crop-ratio 1:1 --face-restoration --output-dir ./my_dataset
personfromvid video3.mp4 --crop-ratio 1:1 --face-restoration --output-dir ./my_dataset
```

### Resume & Processing Control

```bash
# Normal processing automatically resumes from where it left off
personfromvid video.mp4 --crop-ratio 1:1 --face-restoration

# Force restart from beginning (clears previous state)
personfromvid video.mp4 --crop-ratio 1:1 --face-restoration --force

# Keep extracted frames and data files between runs (useful for incremental processing)
personfromvid video.mp4 --crop-ratio 1:1 --face-restoration --keep-temp
```

**💡 Pro Tips for Dataset Creation:**
- Use `--crop-ratio 1:1` for square crops (most compatible with ML models)
- Enable `--face-restoration` for higher quality faces in your dataset
- Set `--resize 512` or `--resize 768` for consistent resolutions
- Processing is **resumable** - interrupted sessions automatically continue where they left off
- Use `--force` to restart processing from the beginning when needed

### Basic Usage

```bash
# Simple processing (saves to video's directory)
personfromvid video.mp4

# Specify output directory
personfromvid video.mp4 --output-dir ./extracted_frames

# Use GPU for faster processing (recommended for large datasets)
personfromvid video.mp4 --device gpu

# Verbose output for monitoring progress
personfromvid video.mp4 --verbose
```

### Advanced Crop Options

```bash
# Variable aspect ratio crops (preserve natural proportions)
personfromvid video.mp4 --crop-ratio any --crop-padding 0.2

# Widescreen crops with full frames included
personfromvid video.mp4 --crop-ratio 16:9 --full-frames --output-dir ./widescreen

# Custom padding and high-quality output
personfromvid video.mp4 --crop-ratio 1:1 --crop-padding 0.3 --output-jpg-quality 98

# Large dataset processing with limits
personfromvid video.mp4 \
    --crop-ratio 1:1 \
    --face-restoration \
    --max-frames 1000 \
    --batch-size 16 \
    --device gpu
```

**Crop Ratio Options:**
- **`1:1`** (square): Most common for ML training, ensures consistent dimensions
- **`16:9`** (widescreen): Good for cinematic shots, wider context
- **`4:3`** (portrait): Better for full-body poses, traditional aspect ratio
- **`any`**: Preserves natural proportions while applying padding
- **Omit `--crop-ratio`**: No cropping, outputs full frames only

### Processing Control

```bash
# Force restart processing (clears previous state)
personfromvid video.mp4 --force

# Keep temporary files for debugging
personfromvid video.mp4 --keep-temp

# Disable face restoration for faster processing
personfromvid video.mp4 --no-face-restoration

# Custom face restoration strength (0.0-1.0)
personfromvid video.mp4 --face-restoration --face-restoration-strength 0.9
```

## Batch Processing Workflows

### Creating Large Datasets

For processing multiple videos with consistent settings, create a simple script or use shell commands:

```bash
# Process all MP4 files in a directory to create a unified dataset
for video in *.mp4; do
    personfromvid "$video" \
        --crop-ratio 1:1 \
        --face-restoration \
        --resize 512 \
        --output-format jpg \
        --output-dir ./training_dataset
done

# Or using find for recursive processing
find ./videos -name "*.mp4" -exec personfromvid {} \
    --crop-ratio 1:1 \
    --face-restoration \
    --output-dir ./dataset \
    --device gpu \;
```

### Configuration File Approach

For consistent settings across multiple runs, use a configuration file:

```yaml
# dataset_config.yaml
output:
  image:
    format: "jpg"
    jpg:
      quality: 95
    crop_ratio: "1:1"
    face_restoration_enabled: true
    resize: 512
models:
  device: "gpu"
  batch_size: 8
```

Then process videos:
```bash
personfromvid video1.mp4 --config dataset_config.yaml --output-dir ./dataset
personfromvid video2.mp4 --config dataset_config.yaml --output-dir ./dataset
personfromvid video3.mp4 --config dataset_config.yaml --output-dir ./dataset
```

### Quality Control and Dataset Curation

```bash
# High-quality settings for final dataset
personfromvid video.mp4 \
    --crop-ratio 1:1 \
    --face-restoration \
    --resize 768 \
    --output-jpg-quality 98 \
    --quality-threshold 0.4 \
    --confidence 0.5 \
    --max-frames-per-category 8

# Quick preview with lower quality for initial review
personfromvid video.mp4 \
    --crop-ratio 1:1 \
    --resize 256 \
    --max-frames 50 \
    --output-dir ./preview
```

## Command-line Options

`personfromvid` offers many options to customize its behavior. Here are the available options:

### General Options
| Option | Alias | Description | Default |
| --- | --- | --- | --- |
| `--config` | `-c` | Path to a YAML or JSON configuration file. | `None` |
| `--output-dir` | `-o` | Directory to save output files. | Video's directory |
| `--log-level` | `-l` | Set logging level (`DEBUG`, `INFO`, `WARNING`, `ERROR`). | `INFO` |
| `--verbose` | `-v` | Enable verbose output (sets log level to `DEBUG`). | `False` |
| `--quiet` | `-q` | Suppress non-essential output. | `False` |
| `--no-structured-output` | | Disable structured output format (use basic logging). | `False` |
| `--version` | | Show version information and exit. | `False` |

### AI Model Options
| Option | Description | Default |
| --- | --- | --- |
| `--device` | Device to use for AI models (`auto`, `cpu`, `gpu`). | `auto` |
| `--batch-size` | Batch size for AI model inference (1-64). | `1` |
| `--confidence` | Confidence threshold for detections (0.0-1.0). | `0.3` |

### Frame Processing Options
| Option | Description | Default |
| --- | --- | --- |
| `--max-frames` | Maximum frames to extract per video. | `None` |
| `--quality-threshold` | Quality threshold for frame selection (0.0-1.0). | `0.2` |

### Output Options
| Option | Description | Default |
| --- | --- | --- |
| `--output-format` | Output image format (`jpg` or `png`). | `jpg` |
| `--output-jpg-quality` | Quality for JPG output (70-100). | `95` |
| `--output-face-crop-enabled` / `--no-output-face-crop-enabled` | Enable or disable generation of cropped face images. | `True` |
| `--output-face-crop-padding` | Padding around face bounding box (0.0-1.0). | `0.3` |
| `--face-restoration` / `--no-face-restoration` | Enable/disable GFPGAN face restoration for enhanced quality (faster than Real-ESRGAN, optimized for faces). | `False` |
| `--face-restoration-strength` | Face restoration strength: 0.0=no effect, 1.0=full restoration, 0.8=recommended balance (0.0-1.0). | `0.8` |
| `--crop-ratio` | Aspect ratio for crops: fixed ratios (e.g., '1:1', '16:9', '4:3') or 'any' for variable aspect ratios. Automatically enables cropping. | `None` |
| `--crop-padding` | Padding around pose bounding box for crops (0.0-1.0). | `0.1` |
| `--full-frames` | Output full frames in addition to crops when cropping is enabled. | `False` |
| `--output-png-optimize` / `--no-output-png-optimize` | Enable or disable PNG optimization. | `True` |
| `--resize` | Maximum dimension for proportional resizing (256-4096 pixels). | `None` |
| `--min-frames-per-category` | Minimum frames to output per pose/angle category (1-10). | `3` |
| `--max-frames-per-category` | Maximum frames to output per pose/angle category (1-100). | `5` |

### Processing Control Options
| Option | Description | Default |
| --- | --- | --- |
| `--force` | Force restart analysis by deleting existing state. | `False` |
| `--keep-temp` | Keep temporary files after processing. | `False` |

For a full list of options, run `personfromvid --help`.

## Output Structure

Person From Vid creates dataset-friendly output with descriptive filenames and organized structure. By default, files are saved to the video's directory, but `--output-dir` lets you create unified datasets from multiple videos.

### Example Output for `interview.mp4`:

```
interview_info.json                         # Processing metadata and frame details
interview_standing_front_closeup_001.jpg   # Full frame: {video}_{pose}_{head}_{shot}_{rank}
interview_sitting_profile-left_medium-shot_002.jpg
interview_face_front_001.jpg               # Face crop: {video}_face_{head-angle}_{rank}
interview_face_profile-right_002.jpg
interview_crop_standing_front_001.jpg      # Pose crop: {video}_crop_{pose}_{head}_{rank}
interview_crop_sitting_profile-left_002.jpg
```

### Dataset-Friendly Organization

When using `--output-dir` for multiple videos, files are naturally organized:

```bash
# Process multiple videos to the same dataset directory
personfromvid video1.mp4 --crop-ratio 1:1 --output-dir ./my_dataset
personfromvid video2.mp4 --crop-ratio 1:1 --output-dir ./my_dataset

# Results in organized dataset:
my_dataset/
├── video1_crop_standing_front_001.jpg
├── video1_crop_sitting_profile-left_002.jpg
├── video1_face_front_001.jpg
├── video2_crop_standing_front_001.jpg
├── video2_crop_sitting_profile-right_002.jpg
└── video2_face_front_001.jpg
```

### File Types
- **`{video}_info.json`**: Processing metadata, configuration, and frame analysis details
- **Full Frame Images**: Complete frames with pose, head orientation, and shot type in filename
- **Face Crops**: Cropped and optionally restored faces with head orientation details
- **Pose Crops**: Body/pose crops with consistent aspect ratios for ML training

## Configuration

Person From Vid can be configured via a YAML file, environment variables, or command-line arguments.

### Configuration File

Create a YAML file (e.g., `config.yaml`) to manage settings. CLI arguments will override file settings.

```yaml
# config.yaml

# AI Models and device settings
models:
  device: "auto"  # "cpu", "gpu", or "auto"
  batch_size: 1
  confidence_threshold: 0.3
  face_detection_model: "yolov8s-face"
  pose_estimation_model: "yolov8s-pose"
  head_pose_model: "sixdrepnet"

# Frame extraction strategy
frame_extraction:
  temporal_sampling_interval: 0.25 # Seconds between samples
  enable_keyframe_detection: true
  enable_temporal_sampling: true
  max_frames_per_video: null # No limit

# Quality assessment thresholds
quality:
  blur_threshold: 100.0
  brightness_min: 30.0
  brightness_max: 225.0
  contrast_min: 20.0
  enable_multiple_metrics: true

# Pose classification thresholds
pose_classification:
  standing_hip_knee_angle_min: 160.0
  sitting_hip_knee_angle_min: 80.0
  sitting_hip_knee_angle_max: 120.0
  squatting_hip_knee_angle_max: 90.0
  closeup_face_area_threshold: 0.15

# Head angle classification
head_angle:
  yaw_threshold_degrees: 22.5
  pitch_threshold_degrees: 22.5
  max_roll_degrees: 30.0
  profile_yaw_threshold: 67.5

# Closeup detection settings
closeup_detection:
  extreme_closeup_threshold: 0.25
  closeup_threshold: 0.15
  medium_closeup_threshold: 0.08
  medium_shot_threshold: 0.03
  shoulder_width_threshold: 0.35
  enable_distance_estimation: true

# Frame selection criteria
frame_selection:
  min_quality_threshold: 0.2
  face_size_weight: 0.3
  quality_weight: 0.7
  diversity_threshold: 0.8
  temporal_diversity_threshold: 3.0  # Minimum seconds between selected frames

# Output settings
output:
  min_frames_per_category: 3
  max_frames_per_category: 5
  preserve_metadata: true
  image:
    format: "jpg"
    jpg:
      quality: 95
    png:
      optimize: true
    face_crop_enabled: true
    face_crop_padding: 0.3
    face_restoration_enabled: false  # Enable GFPGAN face restoration
    face_restoration_strength: 0.8   # Restoration strength (0.0-1.0)
    enable_pose_cropping: true
    crop_ratio: any  # Aspect ratio for crops: fixed ratios (e.g., "1:1", "16:9", "4:3") or "any" for variable aspect ratios. Automatically enables cropping.
    pose_crop_padding: 0.1  # Padding around pose bounding box for crops
    full_frames: false  # Output full frames in addition to crops when enable_pose_cropping is true

# Storage and caching
storage:
  cache_directory: "~/.cache/personfromvid"  # Override default cache location
  temp_directory: null                       # Auto-generated if null
  keep_temp: false                           # Keep temporary files after processing
  force_temp_cleanup: false                  # Force cleanup before starting
  cleanup_temp_on_success: true              # Clean up temp files on success
  cleanup_temp_on_failure: false             # Keep temp files if processing fails
  max_cache_size_gb: 5.0

# Processing behavior
processing:
  force_restart: false                       # Force restart by deleting existing state
  save_intermediate_results: true
  max_processing_time_minutes: null         # No time limit
  parallel_workers: 1

# Logging configuration
logging:
  level: "INFO" # DEBUG, INFO, WARNING, ERROR, CRITICAL
  enable_file_logging: false
  log_file: null
  enable_rich_console: true
  enable_structured_output: true
  verbose: false

# Person-based selection criteria (enhanced for multi-person support)
person_selection:
  enabled: true  # Use person-based selection (recommended for multi-person videos)
  min_instances_per_person: 3
  max_instances_per_person: 10
  min_quality_threshold: 0.3
  temporal_diversity_threshold: 3.0  # Minimum seconds between ALL selected instances (applies to both minimum and additional selections for better temporal diversity)
```

Use with:
```bash
personfromvid video.mp4 --config config.yaml
```

## Development

### Setting Up Development Environment

```bash
# Clone repository
git clone https://github.com/personfromvid/personfromvid.git
cd personfromvid

# Create virtual environment
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate

# Install development dependencies
pip install -e ".[dev]"

# Install pre-commit hooks
pre-commit install
```

### Project Structure

```
personfromvid/
├── personfromvid/           # Main package
│   ├── cli.py              # Command-line interface
│   ├── core/               # Core processing modules
│   ├── models/             # AI model management
│   ├── analysis/           # Image analysis and classification
│   ├── output/             # Output generation
│   ├── utils/              # Utility modules
│   └── data/               # Data models and configuration
├── tests/                  # Test suite
├── docs/                   # Documentation
└── scripts/                # Development scripts
```

### Running Tests

```bash
# Run all tests
pytest

# Run with coverage
pytest --cov=personfromvid

# Run specific test modules
pytest tests/unit/test_config.py
```

### Code Quality

```bash
# Format code
black personfromvid/

# Check linting
flake8 personfromvid/

# Type checking
mypy personfromvid/
```

### Cleaning Up

To remove temporary files, build artifacts, and caches, run the cleaning script:

```bash
python scripts/clean.py
```

## System Requirements

### Minimum Requirements
- Python 3.10+
- 4GB RAM
- 1GB disk space for dependencies and cache
- FFmpeg

### Recommended Requirements
- Python 3.11+
- 8GB+ RAM
- 5GB+ disk space for cache
- NVIDIA GPU with CUDA support for acceleration
- FFmpeg with hardware acceleration support

## Supported Formats

### Video Formats
- MP4, AVI, MOV, MKV, WMV, FLV, WebM, M4V, 3GP, OGV

### Output Formats
- JPG images (configurable quality)
- PNG images (configurable quality)
- JSON metadata files

## Cache and Temporary Files

Person From Vid uses a centralized cache directory to store both AI models and temporary files during video processing. This keeps your video directories clean and makes cache management easier.

### Cache Directory Locations

The cache directory is automatically determined based on your operating system:

- **Linux**: `~/.cache/personfromvid/`
- **macOS**: `~/Library/Caches/personfromvid/`
- **Windows**: `C:\Users\{username}\AppData\Local\codeprimate\personfromvid\Cache\`