# Training record and optional new run

The supplied `outputs/nnunet/model.pt` is nnU-Net v2 ResEnc M trained without
external pretrained weights. The initial 50-epoch run used 32 official
TotalSegmentator training scans. Later training used 64 training scans and
focused more often on partial L2 examples. The selected continuation is epoch
15, at 17,500 updates in total. It was frozen using all 57 official source
validation scans before the final test evaluation. See `REPORT.pdf` for results.

The historical intermediate checkpoints are not bundled. The training launcher
below starts a *new* model from random weights; it does not recreate the supplied
checkpoint or its measured scores.
The launcher checks the official train, validation and test split in the
dataset's `meta.csv`. It writes the selected case IDs and new normalization
values in the new training work directory.

## Run a new model on the full official training split

Install the pinned training dependencies in Python 3.11:

```bash
python3.11 -m venv .venv
.venv/bin/python -m pip install -r requirements-nnunet.txt
```

Then start training from this folder:

```bash
LUMBAR_PYTHON="$PWD/.venv/bin/python" ./run_training.sh --data-dir /path/to/totalsegmentator_v201 --device auto
```

The launcher attempts all 1,082 official training scans and records any rejected
by the existing label and geometry checks. All 57 official validation scans stay
separate. TotalSegmentator test and VerSe are never used for training or model
selection. Training data and prepared caches are not included in this package.

`auto` chooses CUDA when available, otherwise CPU. You may request `cuda`,
`mps`, or `cpu`; MPS is accepted only if this PyTorch build supports the required
3D forward and backward operations. CPU training is slow. The architecture and
2 mm preprocessing remain unchanged across devices.

Use `--check` to verify paths and dependencies without starting a run. New
outputs go under `training/full_source/` by default; `--work-dir` chooses another
new location. The launcher does not replace the supplied inference weights.
It first checks recovery of all five labels on a training patch, discards those
diagnostic weights, then trains from scratch. It compares the midway and final
checkpoints on full-volume source validation and records per-level scores and
failures. A failed learning check does not promote a new `model.pt`.

The launcher uses `run_full_training.py`, `prepare_full_source.py`,
`prepare_nnunet.py`, `train_nnunet.py`, `export_nnunet.py`, and
`validate_model.py` in `scripts/`. No new full-source training result has been
measured or used for the supplied report.
