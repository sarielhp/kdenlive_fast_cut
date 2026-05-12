# Changelog

All notable changes to this project will be documented in this file.

## [0.1] - 2026-05-12

### Added
- **Smart Cut Engine**: Core logic to parse `.kdenlive` XML and extract video segments for direct stream copying using FFmpeg.
- **Multiple Rendering Tiers**:
    - `--hf`: Fastest YouTube-compatible flatten (CRF 23, ultrafast).
    - `--hf2`: Balanced YouTube-compatible flatten (CRF 20, superfast).
    - `--hf3`: Efficient YouTube-compatible flatten (CRF 23, veryfast). **[DEFAULT]**
    - `--hybrid`: Direct stream copy without flattening (Fastest, not for YouTube).
    - `--av1`: High-efficiency SVT-AV1 re-encode.
    - `--fast`: Fast H.264 re-encode.
    - `--insane`: Lossless H.264 re-encode.
- **Progress & Feedback**:
    - Real-time progress bars with percentage and ETR (Estimated Time Remaining) for both `melt` and `ffmpeg` (flattening) stages.
    - Reusable `show_progress` UI helper.
- **Analytics & Reporting**:
    - **Execution Timing**: Natural time formatting (e.g., `1m 05s`, `8s`). Reports duration for every command taking > 5s and always reports total duration.
    - **File Size Reporting**: Project-aware calculation that sums all unique input media resources vs. the final output size in MB.
- **Documentation**:
    - Comprehensive `README.md` with technical justification vs. standard NLE rendering, typical workflow, and YouTube compatibility warnings.
    - Detailed internal help menu (`-h`).
- **Project Structure**: Initialized as a Git repository with versioning (v0.1).
