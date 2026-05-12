# kdenlive_fast_cut

A high-performance rendering wrapper for Kdenlive projects, optimized for speed and YouTube compatibility.

## The "Fast Cut" Advantage

Unlike standard Kdenlive rendering which **re-encodes every single frame** of your project (a slow and CPU-intensive process), `kdenlive_fast_cut` uses a "Smart Cutting" approach:

1. **Video Stream Copy**: For segments where no effects are applied, it copies the original video data directly. This is near-instant and preserves 100% of the original quality.
2. **Audio Sync**: It generates a clean, frame-accurate audio track to ensure perfect synchronization.
3. **Optional Flattening**: To ensure compatibility with platforms like YouTube (which do not support MP4 Edit Lists), it can "flatten" the resulting cut into a standard H.264 file at extremely high speeds.

This makes it the ideal tool for **Zoom recordings, slide talks, and simple edits** where full re-encoding is a waste of time and energy.

## Features

- **Blazing Fast**: Up to 10x-50x faster than traditional rendering for simple cuts.
- **YouTube Compatible**: HF modes ensure your videos work perfectly on YouTube.
- **Project-Aware Reporting**: Displays total source media size vs. final output size in MB.
- **Real-time Feedback**: Beautiful progress bars with Estimated Time Remaining (ETR) for all stages.
- **Natural Timing**: Displays exactly how long each stage took (if > 5s) and the total overall time.

## Usage

```bash
./kdenlive_fast_cut [OPTIONS] project.kdenlive
```

### Options

| Option | Description | Recommended For |
| :--- | :--- | :--- |
| (None) | **Default: --hf3** | Best for YouTube, balanced size/speed. |
| `--hf` | Fastest Flatten (CRF 23, ultrafast) | When you need it NOW. |
| `--hf2` | Balanced Flatten (CRF 20, superfast) | High quality slides/text. |
| `--hf3` | Efficient Flatten (CRF 23, veryfast) | Archiving and sharing. |
| `--hybrid`| Direct Smart Cut (No flattening) | Local playback only (Not for YouTube). |
| `--av1` | Full AV1 Re-encode | Maximum compression (Very slow). |
| `--fast` | Full H.264 Re-encode | Fast full-project re-encoding. |

## Requirements

- **Ruby**
- **FFmpeg** (with `ffprobe`)
- **MLT** (`melt` command)
- **Rainbow** (Ruby gem: `gem install rainbow`)

## Installation

Simply copy the script and ensure it is executable:
```bash
chmod +x kdenlive_fast_cut
```
