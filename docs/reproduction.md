# Reproduction notes

## Match the submitted implementation

Use the original team's [restoration commit `bbbd85b`](https://github.com/rabrie10/Gforce-ai-cup-2026/commit/bbbd85b) or the reviewed team revision `9dc81a1`. Both contain the same restored `v5/gpu/pipeline_v6.py` bytes. The submission-time Git commit `f3c247f` contains the experimental camera clamp. The restored Git file has SHA-256 `984303a2153902e7466868fd425dd3d1c9d20bb893a01bf3a5353c3ce5d0e14c` with LF line endings.

Run commands below from `drone-flyby/` in the original team checkout. Use `9dc81a1` for regression checks because it includes the post-submission test-fixture fix. The [organizer's starter repository](https://github.com/amboltio/Nordic-AI-Cup-2026/tree/main/drone-flyby) documents the wire protocol and the evaluator. The staged code extraction keeps these organizer files external pending permission.

## Model-free checks

Set up an isolated Python environment, install the packages from the team's `requirements.txt`, and install `pytest`. The following tests exercise synthetic sequences, output validation, and capture behavior on CPU. The ground-truth scorer check uses the authorized Helsinki reference scene from the original checkout.

```bash
python -m pytest -q tests/
PYTHONPATH=. python v5/gpu/tests/test_camera_v6.py
python local_evaluator.py --scene helsinki --oracle
```

The oracle should yield mAP@0.50 of 1.000 by feeding the reference annotations to the local scorer. It checks the scorer and data contract.

The review ran the four files in `tests/` against team main on Linux / Python 3.12 with CPU PyTorch: **57 passed**. The standalone camera test passed four 160-frame lag simulations with zero illegal requests. The Helsinki ground-truth oracle returned **1.000 mAP@0.50**. The review used the upstream checkout for these checks because the draft extraction omits organizer code and data.

## Historical model replay

Exact model replay requires the team's archived epoch-15 YOLO11s checkpoint, the DINOv2 ONNX encoder, the 16-class visual head, and the background head. Target the original Python 3.11 environment with a compatible CUDA PyTorch build, CUDA-enabled ONNX Runtime and its required CUDA libraries. The archived models, reference frames, and hosted captures are outside this review repository.

```bash
export V6_DETECTOR=/absolute/path/to/v7_e15_snapshot.pt
export V5_ASSETS=/absolute/path/to/encoder-and-heads
export V5_AZURE_VM=1  # Historical Linux runtime opt-in required by the encoder loader
export V5_DEVICE=cuda:0 V6_CLASSIFY_MIN_PX=16 V6_TARGET_MIN=0.5 V6_DET_CONF=0.15
export V6_ENSEMBLE=0 V6_DIAGNOSTIC_CAPTURE=0
uvicorn v5.gpu.endpoint_v6:app --host 127.0.0.1 --port 9053 --workers 1
```

Keep one worker because track and camera state live in memory. Check readiness via `/`, then run `python local_evaluator.py --url http://127.0.0.1:9053/predict --scene helsinki` from another terminal. Add `--realtime` to exercise the local 3 FPS frame clock. The official held-out evaluation was performed by the organizer.

For training, see the [team's V7 training recipe](https://github.com/rabrie10/Gforce-ai-cup-2026/blob/main/drone-flyby/SOLUTION_README.md#reproducing). It documents mask extraction, the six seeded shards, training, and gate ablations. The scripts contain host-specific paths that require configuration. Bit-identical dataset regeneration and exact model replay remain unverified in this review.
