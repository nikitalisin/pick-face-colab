# PickFace for Google Colab

Choose a face, preview its mask, and generate a video from a source clip and separate speech audio, all inside Colab.

[Open PickFace v5.5 in Colab](https://colab.research.google.com/github/nikitalisin/pick-face-colab/blob/main/PickFace_Colab_v5_5_EN.ipynb)

1. Use a fresh A100 GPU runtime and run setup steps 1–6.
2. Step 2 downloads and verifies the source archive automatically. No manual archive upload or Google Drive is required.
3. Upload your video and speech audio via the Colab **Files** sidebar. In step 7, paste each **Copy path** value, choose 480p or 720p, and click **Use files**.
4. In step 8, click **Detect faces**, choose **Face ID**, then **Preview selected face**. Use **Frame** to inspect tracking.
5. Click **Generate video**. Play or download the result in step 9.

The preview starts at the first propagated frame (frame 2 for this workflow). Face IDs are not left-to-right positions.

## Source archive

The `sources-v1` release contains the frozen source bundle. Demo video and audio files are excluded. Third-party source notices and licenses are retained in the archive. Model weights are downloaded separately by the notebook.

Archive SHA-256:
`feaac41ef22b9e5db71e825979a9fc6035cb380a154005cd1f4670cd44e4f403`

BF16 + SDPA were used in the successful v4 A100 generation. Offline checks cover the new controls, graph isolation, selection checks and archive integrity; the full v5.5 Colab run still needs validation.

If a wait is interrupted, use **Recover last job**. If a job fails, use **Download diagnostics** in step 9.

## Resolution and orientation

The longest side is **1280 pixels in 720p mode**, or **832 pixels in 480p mode**.
Landscape, portrait and square videos are supported automatically. Step 7 shows the exact output size.
Dimensions are rounded to multiples of 16, so a few edge pixels may be cropped.
For 16:9 / 9:16 / square inputs:

| Mode | Landscape | Portrait | Square |
| --- | --- | --- | --- |
| 720p | 1280 × 720 | 720 × 1280 | 1280 × 1280 |
| 480p | 832 × 464 | 464 × 832 | 832 × 832 |

Square frames contain more pixels and may use more GPU memory than widescreen frames at the same longest side.
Changing video or resolution requires detecting and selecting the face again. BF16 + SDPA remain enabled.

## Fifteen-second test (v5.5)

Processes up to 15 seconds at 25 FPS, capped at 375 input frames. Supply video and speech audio at least 15 seconds long.
Wan may trim a few frames; short inputs are not extended.
The sampler window remains 121 frames, motion 9, drop 8. The v5.4 mask alignment fix and download retries are retained.
Use a fresh runtime.

A user confirmed a successful 10-second v5.4 test without a visible window seam; peak GPU memory was not measured.
V5.5 passed offline graph and decoder checks for landscape, portrait and square inputs.
Full 15-second generation, new output shapes and peak A100 memory still need validation.
