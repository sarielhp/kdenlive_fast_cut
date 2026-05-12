# kdenlive_fast_cut

A high-performance rendering wrapper for Kdenlive projects, optimized for speed and YouTube compatibility.

## The "Fast Cut" Advantage

**Note:** This program was specifically designed for uploading **recorded talks (usually Zoom recordings) to YouTube** with maximum efficiency.

Unlike standard Kdenlive rendering which **re-encodes every single frame** of your project (a slow and CPU-intensive process), `kdenlive_fast_cut` uses a "Smart Cutting" approach:

1. **Video Stream Copy**: For segments where no effects are applied, it copies the original video data directly. This is near-instant and preserves 100% of the original quality.
2. **Audio Sync**: It generates a clean, frame-accurate audio track to ensure perfect synchronization.
3. **Optional Flattening**: To ensure compatibility with platforms like YouTube (which do not support MP4 Edit Lists), it can "flatten" the resulting cut into a standard H.264 file at extremely high speeds.

## Why not just use Kdenlive?

You might wonder if this tool is necessary given that Kdenlive is a powerful editor. The answer lies in the difference between **Rendering** and **Splicing**:

- **Kdenlive is an NLE**: Its engine (MLT) is designed for complex compositing and effects. Because of this, Kdenlive **always re-encodes every pixel** of your video during a render, even if you haven't applied any effects. This is a slow, CPU-intensive process.
- **Smart Cutting**: `kdenlive_fast_cut` performs "Smart Cutting" at the bitstream level. It identifies the exact segments in your `.kdenlive` XML and uses `ffmpeg` to copy the video streams directly. This bypasses the entire rendering pipeline, moving the bottleneck from your CPU's encoding speed to your disk's I/O speed.
- **The "Lossless" Problem**: While Kdenlive offers "lossless" profiles, they still require a full render pass (slow) and produce massive file sizes (hundreds of gigabytes). This tool gives you the original quality and size at the speed of a file copy.

For Zoom recordings, slide talks, and simple edits, `kdenlive_fast_cut` is a surgical tool that saves hours of unnecessary rendering time.

## Disclaimers & Limitations

This script is a specialized tool for **fast splicing**. It does **NOT** support the full range of Kdenlive features:

- **No Effects/Transitions**: Any transitions (dissolves, wipes) or effects applied in the Kdenlive timeline will be **ignored**.
- **No Titles/Overlays**: Kdenlive title clips or overlays will not be rendered.
- **Single Track Focus**: The script focuses on the primary video track. Complex multi-track compositing is not supported.

If your project requires effects, titles, or complex compositing, you should use Kdenlive's built-in **Render** button.

## Features

- **Blazing Fast**: Up to 10x-50x faster than traditional rendering for simple cuts.
- **YouTube Compatible**: HF modes ensure your videos work perfectly on YouTube.
- **Project-Aware Reporting**: Displays total source media size vs. final output size in MB.
- **Real-time Feedback**: Beautiful progress bars with Estimated Time Remaining (ETR) for all stages.
- **Natural Timing**: Displays exactly how long each stage took (if > 5s) and the total overall time.

## Typical Workflow

1. **Edit in Kdenlive**: Open your video in Kdenlive. Use the timeline to cut out the parts you don't want (like "dead air," mistakes, or sensitive info).
2. **Save Project**: Simply save your project as a `.kdenlive` file. You do **not** need to click the "Render" button in Kdenlive.
3. **Fast Cut**: Run this script on the saved project file:
   ```bash
   ./kdenlive_fast_cut your_project.kdenlive
   ```
The script will analyze your timeline and produce a high-quality MP4 almost instantly.

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
