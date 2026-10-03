# PickFace for Google Colab

Choose a face, preview its mask, and generate a video from a source clip and separate speech audio, all inside Colab.

[Open PickFace v5.2 in Colab](https://colab.research.google.com/github/nikitalisin/pick-face-colab/blob/main/PickFace_Colab_v5_2_EN.ipynb)

1. Use a fresh A100 GPU runtime and run setup steps 1–6.
2. Step 2 downloads and verifies the source archive automatically. No manual archive upload or Google Drive is required.
3. Upload your video and speech audio in step 7.
4. In step 8, click **Detect faces**, choose **Face ID**, then **Preview selected face**. Use **Frame** to inspect tracking.
5. Click **Generate video**. Play or download the result in step 9.

The preview starts at the first propagated frame (frame 2 for this workflow). Face IDs are not left-to-right positions.

## Source archive

The `sources-v1` release contains the frozen source bundle. Demo video and audio files are excluded. Third-party source notices and licenses are retained in the archive. Model weights are downloaded separately by the notebook.

Archive SHA-256:
`feaac41ef22b9e5db71e825979a9fc6035cb380a154005cd1f4670cd44e4f403`

BF16 + SDPA were used in the successful v4 A100 generation. Offline checks cover the new controls, graph isolation, selection checks and archive integrity; the full v5.2 Colab run still needs validation.

If a wait is interrupted, use **Recover last job**. If a job fails, use **Download diagnostics** in step 9.
