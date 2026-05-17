# AbstainGemma 🛑

> **Calibrated uncertainty for Gemma 4 vision. When unsure, we say so — with statistical guarantees.**

[![Apache 2.0](https://img.shields.io/badge/license-Apache%202.0-blue)](LICENSE)
[![Gemma 4](https://img.shields.io/badge/Gemma-4--E4B-orange)](https://huggingface.co/google/gemma-4-E4B-it)

Built for the [Gemma 4 Good Hackathon](https://www.kaggle.com/competitions/gemma-4-good-hackathon) — Safety & Trust track.

## TL;DR

Most vision-language models are confidently wrong. AbstainGemma wraps off-the-shelf Gemma 4 E4B with **self-consistency scoring** and **split-conformal calibration** — adding a calibrated "I don't know" with finite-sample coverage guarantees. **No fine-tuning required.**

## Headline result (200 examples)

| Dataset | Baseline | AbstainGemma | Abstention | Gain |
|---|---|---|---|---|
| VizWiz (n=30) | 73.3% | **84.0%** | 17% | **+10.7 pp** |
| **OK-VQA (n=30)** | 40.0% | **69.2%** | 57% | **+29.2 pp** |
| **Combined (n=60)** | **56.7%** | **78.95%** | 37% | **+22.3 pp** |

At an abstention rate of 37%, AbstainGemma boosts accuracy on kept answers from 56.7% to **78.95%** — a **39% relative improvement** with no model retraining.

Operating point: α=0.20, τ=0.582 (split conformal on n=140 held-out calibration set).

## Method

1. **Sample** N=4 stochastic answers from Gemma 4 E4B at temperature 0.7
2. **Score** pairwise normalization-robust similarity → self-consistency s ∈ [0,1]
3. **Calibrate** threshold τ via split conformal (Vovk 2005) at α=0.20
4. **Abstain** when s < τ

## Datasets

- **VizWiz-VQA** (Gurari et al., CVPR 2018) — 100 examples; real photos from blind users
- **OK-VQA** (Marino et al., CVPR 2019) — 100 examples; knowledge-based reasoning

## Reproduce

Open `abstain_gemma_clean.ipynb` in Colab Pro (A100). Run all cells. ~10 minutes wall time.

## Files

- `abstain_gemma_clean.ipynb` — full reproducible pipeline
- `final_report.json` — calibration results + trust-coverage curve
- `results_day2.json`, `results_okvqa.json` — 200 examples with stochastic samples + consistency scores
- `vizwiz_samples.json`, `okvqa_samples.json` — dataset metadata

## Limitations

- **N=200 is small.** Confidence intervals on per-dataset numbers are wide.
- **α=0.20 is a tunable knob.** The trust-coverage curve shows the trade-off across α∈[0.10, 0.40].
- **English-only evaluation.**
- **Similarity is heuristic.** Token-Jaccard + character 3-gram overlap.
- **Self-consistency is necessary but not sufficient.** A model can be consistent AND wrong (e.g., misreading "062C" as "B062C"). Future work: combine with verifier models.

## License

Apache 2.0. Gemma 4 weights remain under [Gemma terms of use](https://ai.google.dev/gemma/terms).
