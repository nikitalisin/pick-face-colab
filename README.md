# PickFace for Google Colab

Choose a face, preview its mask, and generate a video from a source clip and separate speech audio, all inside Colab.

[Open PickFace v5.11 in Colab](https://colab.research.google.com/github/nikitalisin/pick-face-colab/blob/main/PickFace_Colab_v5_11_EN.ipynb)

1. Use a fresh A100 GPU runtime and run setup steps 1–6.
2. Step 2 downloads and verifies the source archive automatically. No manual archive upload or Google Drive is required.
3. Upload your video and speech audio via the Colab **Files** sidebar. In step 7, paste each **Copy path** value, choose 480p or 720p, and click **Use files**.
4. In step 8, select **Detect faces**, then run **step 8b**. Choose **Face ID**, select **Preview selected face**, then run **8b** again. Use **Frame** to inspect tracking.
5. Select **Generate video** in step 8, then run **8b** and leave it executing until completion. Play or download the result in step 9.

The preview starts at the first propagated frame (frame 2 for this workflow). Face IDs are not left-to-right positions.

## Source archive

The `sources-v1` release contains the frozen source bundle. Demo video and audio files are excluded. Third-party source notices and licenses are retained in the archive. Model weights are downloaded separately by the notebook.

Archive SHA-256:
`feaac41ef22b9e5db71e825979a9fc6035cb380a154005cd1f4670cd44e4f403`

BF16 + SDPA were used in the successful v4 A100 generation. Offline checks cover the new controls, graph isolation, selection checks and archive integrity; the full v5.6 Colab run still needs validation.

If a wait is interrupted and the server still exists, select **Recover last job** and run **8b**. If a job fails, use **Download diagnostics** in step 9.

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

## Twenty-second test (v5.7)

Processes up to 20 seconds at 25 FPS, capped at 500 input frames. Supply video and speech audio at least 20 seconds long.
Wan may trim a few frames; short inputs are not extended.
The sampler window remains 121 frames, motion 9, drop 8. The v5.4 mask alignment fix and download retries are retained.
Use a fresh runtime.

A user confirmed a successful 10-second v5.4 test without a visible window seam; peak GPU memory was not measured.
V5.5 passed offline graph and decoder checks for landscape, portrait and square inputs.
Full 20-second generation, new output shapes and peak A100 memory still need validation.

## Face selection fix (v5.6)

All detected tracks are selectable, including faces that disappear in some frames. SAM tracking IDs are preserved when mask row order changes. Frames without the selected track use an empty mask, never another face or a union of faces. Unknown IDs are rejected.

Use a fresh runtime and run Detect faces again. Read the numbers from the new preview; do not reuse IDs from an earlier run. Inspect multiple frames: SAM may still lose a track or assign a new ID after an interruption.

CPU regression checks exercise disappearance, changing mask order, new tracks and the actual frozen SAM output node. The user video still needs a Colab run with this version. Automatic orientation and the v5.6 face-selection fix are retained in v5.7.

## Progress and audio settings (v5.7)

Detect faces, Preview selected face and Generate video now display an overall progress bar and a current-stage bar. SAM reports actual processed frames; generation uses ComfyUI step progress across windows. Recover last job resumes progress polling. Overall percentage is estimated from completed stages, not time remaining. It can pause during model loading or a long sampling step. Only successful server completion produces 100%; failures are shown separately.

- `audio_scale = 1.2`
- `audio_cfg_scale = 1.2`
- Up to 20 seconds / 500 input frames at 25 FPS; shorter video or audio still limits the result. Wan may trim a few frames.

CPU checks passed for progress snapshots, frame reporting, cached dependencies, recovery, endpoint outages, failures, face selection and graph settings. The new widget display and full 20-second GPU generation still need a Colab run. Use a fresh A100 runtime.

## Tilted-face speech mask (v5.8)

User diagnostic overlays confirmed that the previous lower-face rectangle missed the mouth of a lying woman and covered the cheek/ear/neck instead. The speech gate now takes the complete selected SAM face mask directly, matching the face-selection preview. The lower-band node is removed from the submitted generation graph. Missing selected faces remain empty all the way to the speech gate.

The 20-second cap, both audio scales at 1.2, orientation handling, progress bars and window alignment remain as in v5.7. Graph regression checks passed; improved articulation and full-face motion still require a generated-video comparison.

## Thirty-second test (v5.9)

V5.9 processes up to **30 seconds at 25 FPS**, capped at **750 input frames**, with audio capped at **30 seconds**. Supply video and speech audio at least 30 seconds long; shorter inputs are not extended and Wan may trim a few frames.

The v5.8 full selected-face mask, audio_scale=1.2, audio_cfg_scale=1.2, automatic orientation, progress reporting and 121-frame sampler window (motion 9, drop 8) are preserved. Use a fresh A100 runtime and repeat face detection and selection. Offline checks cover the generated graphs and embedded Python syntax. Full 30-second GPU generation and peak memory still need validation.

## Forty-five-second test (v5.10)

The current notebook processes up to **45 seconds at 25 FPS**, capped at **1125 input frames**, with audio capped at **45 seconds**. Supply video and speech audio at least 45 seconds long. Short inputs are not extended; Wan may trim a few frames. Start a fresh A100 runtime, run setup steps 1–6, and import files and repeat face detection/selection in steps 7–8.

The selected-face mask, both audio scales at 1.2, orientation, progress bars and sampler window 121 / motion 9 / drop 8 are preserved. Offline validation compares detect, preview and generation graphs with v5.9 and checks notebook and embedded Python syntax. Full 45-second generation and peak pipeline memory have not yet been tested.

The v5.9 user test completed successfully: 23.88 seconds, 597 frames, 1280×720 at 25 FPS on an A100 40 GB. Generation took 21:02 after detection and preview. Sampling reported 18.291 GB allocated and 24.281 GB reserved; these are not full-pipeline peak measurements. The post-run snapshot showed about 11.4 GB of free system RAM out of 89.6 GB. The supplied MP4 confirms duration and dimensions; sampled frames show mouth movement at the beginning and around 11 and 18 seconds. The face is covered by a hand near the end. Audio synchronization and window seams were not systematically reviewed.

## Explicit execution cell (v5.11)

Buttons in step 8 now select an operation. Step 8b submits and waits for that operation in a regular notebook cell, including detection, preview, generation and recovery. It prints real waiting status at 30-second intervals and surfaces failures or interrupted waits. Requests are consumed once; changing inputs requires selecting the action again. The existing server queue check remains in place.

The 45-second graph and all generation settings remain identical to v5.10. This is not a guaranteed cure for Colab disconnections; Colab's handling of long widget callbacks has not been established. No artificial activity or reconnection scripts are used. Results remain on the runtime disk; deleted runtimes and lost sampling state cannot be recovered with this change. Offline control-flow and syntax checks passed; a full Colab GPU test is pending.
