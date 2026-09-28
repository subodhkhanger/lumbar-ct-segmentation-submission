# Lumbar CT segmentation submission

Final take-home submission for L1-L5 segmentation and vertebral body centroid extraction.

- [Read the two-page report](REPORT.pdf).
- [Download the complete submission ZIP with trained weights](https://github.com/subodhkhanger/lumbar-ct-segmentation-submission/releases/download/v1.0.0/lumbar-ct-segmentation.zip).
- [View release files and checksums](https://github.com/subodhkhanger/lumbar-ct-segmentation-submission/releases/tag/v1.0.0).

For inference, extract the complete release ZIP and run the command below from that extracted folder. The checkpoint is included in the release ZIP; GitHub's automatic source archives and a Git clone contain source code only. Training data is excluded from the submission.

## Inference

Install Python 3.11. Put CT `.nii` or `.nii.gz` files directly in an input folder, then run from this folder:

```bash
./run_inference.sh /path/to/input_cts /path/to/empty_output
```

The first run installs its Python packages automatically. It uses CUDA when available, then tries MPS, then CPU. Each scan gets its own output folder with the vertebrae masks, body masks, and centroid coordinates.

For CTs from these releases that lack spatial units in their NIfTI headers, prefix the command with `LUMBAR_UNITS_POLICY=totalsegmentator-v201` or `LUMBAR_UNITS_POLICY=verse2020`.

## Train a new model

You need Python 3.11 and the complete TotalSegmentator v2.0.1 dataset, including `meta.csv` and the case folders. From this folder, run:

```bash
python3.11 -m venv .venv
.venv/bin/python -m pip install -r requirements-nnunet.txt
LUMBAR_PYTHON="$PWD/.venv/bin/python" ./run_training.sh --data-dir /path/to/totalsegmentator_v201 --device auto
```

`auto` selects CUDA if available, otherwise CPU. You can also request `--device cuda`, `mps`, or `cpu`. The new model is saved under `training/full_source/`; the supplied inference weights are left unchanged.
