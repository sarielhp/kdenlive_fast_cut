# kdenlive_fast_cut

A relatively fast rendering wrapper for Kdenlive projects, adjusted for speed
and YouTube compatibility, as long as what you are doing is just
splicing parts of a single clip into a new video.

## The "Fast Cut" Advantage

**Note:** This program was designed for uploading
**recorded talks (usually Zoom recordings) to YouTube** with
improved efficiency.

Unlike standard Kdenlive rendering which **re-encodes every single
frame** of your project (a slow and CPU-intensive process),
`kdenlive_fast_cut` uses a "Smart Cutting" approach:

1. **Video Stream Copy**: For segments where no effects are applied, it copies the original video data directly. This is fast and preserves the original quality.
2. **Audio Sync**: It generates a clean, frame-accurate audio track to ensure reliable synchronization.
3. **Optional Flattening**: To ensure compatibility with platforms like YouTube (which do not support MP4 Edit Lists), it can "flatten" the resulting cut into a standard H.264 file at relatively high speeds.

## Why not just use Kdenlive?

You might wonder if this tool is useful given that Kdenlive is a powerful editor. The answer lies in the difference between **Rendering** and **Splicing**:

- **Kdenlive is an NLE**: Its engine (MLT) is designed for complex compositing and effects. Because of this, Kdenlive **always re-encodes every pixel** of your video during a render, even if you haven't applied any effects. This is a slow, CPU-intensive process.
- **Smart Cutting**: `kdenlive_fast_cut` performs "Smart Cutting" at the bitstream level. It identifies the exact segments in your `.kdenlive` XML and uses `ffmpeg` to copy the video streams directly. This bypasses the rendering pipeline, moving the bottleneck from your CPU's encoding speed to your disk's I/O speed.
- **The "Lossless" Problem**: While Kdenlive offers "lossless" profiles, they still require a full render pass (slow) and produce large file sizes (hundreds of gigabytes). This tool gives you the original quality and size at the speed of a file copy.

For Zoom recordings, slide talks, and simple edits,
`kdenlive_fast_cut` is a specialized tool that saves time by avoiding
unnecessary rendering. It seems to create a video in 3 minutes that
can be uploaded, instead of 20 minutes which is the result of running
kdenlive render command directly. If you do not need the video for
uploading to youtube, there is an even faster --hybrid option that
completes in seconds.

## Disclaimers & Limitations

This script is a specialized tool for **fast splicing**. It does **NOT** support the full range of Kdenlive features:

- **No Effects/Transitions**: Any transitions (dissolves, wipes) or effects applied in the Kdenlive timeline will be **ignored**.
- **No Titles/Overlays**: Kdenlive title clips or overlays will not be rendered.
- **Single Track Focus**: The script focuses on the primary video track. Complex multi-track compositing is not supported.

If your project requires effects, titles, or complex compositing, you should use Kdenlive's built-in **Render** button.

## Features

- **Relatively Fast**: Up to 10x-50x faster than traditional rendering for simple cuts when using the --hybrid option.

- **YouTube Compatible**: The HF modes ensure your videos work
  well on YouTube (one of them is the default mode). Still
  considerably faster than rendering the project using kdenlive
  render command.


## Typical Workflow

1. **Edit in Kdenlive**: Open your video in Kdenlive. Use the timeline to cut out the parts you don't want (like "dead air," mistakes, or sensitive info).
2. **Save Project**: Simply save your project as a `.kdenlive` file. You do **not** need to click the "Render" button in Kdenlive.
3. **Fast Cut**: Run this script on the saved project file:
   ```bash
   ./kdenlive_fast_cut your_project.kdenlive
   ```
The script will analyze your timeline and produce an MP4 relatively quickly.

## Usage

```bash
./kdenlive_fast_cut [OPTIONS] project.kdenlive
```

### Options

| Option | Description | Recommended For |
| :--- | :--- | :--- |
| (None) | **Default: --hf3** | Best for YouTube, balanced size/speed. |
| `--hf` | Fast Flatten (CRF 23, ultrafast) | For faster results. |
| `--hf2` | Balanced Flatten (CRF 20, superfast) | High quality slides/text. |
| `--hf3` | Efficient Flatten (CRF 23, veryfast) | Archiving and sharing. |
| `--hybrid`| Direct Smart Cut (No flattening) | Local playback only (Not for YouTube). |
| `--av1` | Full AV1 Re-encode | Improved compression (Slow). |
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

## Technical Deep Dive: Hybrid & HF Modes

If you are curious about why this tool is relatively fast compared to a standard Kdenlive render, here is the technical breakdown of the two primary modes.

### 1. Hybrid Mode (`--hybrid`)

The "Hybrid" mode is a two-stage process that prioritizes speed and original quality:

- **Segment Extraction**: The tool parses your `.kdenlive` XML to identify the start and end timestamps for every clip on your timeline. It then uses `ffmpeg` to extract these segments using `-c copy`. This means the raw compressed video data is lifted directly from your source file without being decoded or re-encoded.
- **Stream Concatenation**: These extracted segments are then joined together using the `ffmpeg` `concat` demuxer, again using `-c copy`.

**The "Edit List" Problem**: Because real-world video doesn't always cut perfectly on "Keyframes" (I-frames), `ffmpeg` uses a feature called **Edit Lists** (`elst` atoms in the MP4 container) to tell the player exactly which frames to show and which to skip to maintain synchronization. 

While this is technically valid MP4, **YouTube's processing engine does not support Edit Lists**. If you upload a `--hybrid` file to YouTube, you will often find the audio and video are out of sync, or that segments of your video are missing.

### 2. HF Modes (`--hf`, `--hf2`, `--hf3`)

The "HF" (Hybrid + Flatten) modes solve the YouTube compatibility problem while remaining faster than a traditional render.

- **Step 1: Hybrid Cut**: It first performs the same "Hybrid" cut described above to create a temporary file.
- **Step 2: Flattening**: It then runs a "Flattening" pass. This is a high-speed re-encoding of the **concatenated** video stream:
  ```bash
  ffmpeg -i temp.mp4 -c:v libx264 -crf 23 -preset veryfast -c:a copy final.mp4
  ```
- **Why it works**: By re-encoding the video, `ffmpeg` creates a brand new, continuous bitstream with fresh, linear timestamps. This process removes the need for Edit Lists.
- **Why it's still fast**: Even though it's re-encoding, it is 5-10x faster than Kdenlive because it doesn't have to process the complex MLT (Media Lovin' Toolkit) engine. It isn't calculating transitions, titles, or effects; it's just flattening a single, already-assembled stream.

**The Result**: You get a standard, "flat" H.264 MP4 that is compatible with YouTube, mobile devices, and web browsers, delivered relatively quickly compared to a standard render.

Credit: This program was written using vibe programming, via gemini-cli.
