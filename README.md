# Hierarchical Two-Stage BERT for Cyberbullying Detection

Code and data for the paper:

> M. Syukron, N. S. Ramadani, M. I. Firmansyah, and A. E. Putri,
> **"A Hierarchical Two Stage BERT Model for Cyberbullying Detection,"**
> *IJNMT (International Journal of New Media Technology)*, vol. 13, no. 1, pp. 57–64, 2026.
> DOI: [10.31937/ijnmt.v13i1.4730](https://doi.org/10.31937/ijnmt.v13i1.4730)

Many cyberbullying detectors treat all bullying as one class. Subtler forms such as
**denigration** and **exclusion** depend heavily on context, so this project uses a
hierarchical approach on Indonesian tweets:

```
                 tweet
                   │
        ┌──────────▼──────────┐
        │  Stage 1 (binary)   │  IndoBERTweet
        └──────────┬──────────┘
      not_bullying │ bullying
                   ▼
        ┌─────────────────────┐
        │  Stage 2 (3-class)  │  IndoBERTweet
        └─────────────────────┘
   harassment │ denigration │ exclusion
```

Both stages fine-tune [`indolem/indobertweet-base-uncased`](https://huggingface.co/indolem/indobertweet-base-uncased).

## Repository structure

| Path | Description |
|---|---|
| [`notebooks/two_stage_indobert.ipynb`](notebooks/two_stage_indobert.ipynb) | Main notebook: preprocessing, Stage 1 and Stage 2 training and evaluation, two-stage inference. Reproduces the paper's results. |
| [`notebooks/two_stage_vs_direct_4class.ipynb`](notebooks/two_stage_vs_direct_4class.ipynb) | Supplementary run: deduplicated data, one shared split, and a direct 4-class baseline. |
| [`data/dataset.xlsx`](data/dataset.xlsx) | 300 labelled Indonesian tweets (`full_text`, `label`). |

## Dataset

| Label | Count |
|---|---:|
| `not_bullying` | 145 |
| `denigration` | 56 |
| `harrasment` | 50 |
| `exclusion` | 49 |
| **Total** | **300** |

The tweets are public Indonesian tweets that were labelled manually. Usernames (`@...`) have been
replaced with `@user`. The preprocessing step removes mentions in any case, so this does not
affect the models. The label spelling `harrasment` is kept as in the original data.

> ⚠️ The dataset contains offensive language because of the task.

## Results

Main notebook (the run reported in the paper), with a 70 / 15 / 15 stratified split:

| Stage | Test samples | Accuracy | Macro F1 |
|---|---:|---:|---:|
| Stage 1: bullying vs not bullying | 45 | 1.00 | 1.00 |
| Stage 2: harassment / denigration / exclusion | 24 | 0.92 | 0.92 |

Supplementary notebook (deduplicated data, one shared split):

| Model | Test samples | Accuracy | Macro F1 |
|---|---:|---:|---:|
| Two-stage: Stage 1 | 44 | 1.00 | 1.00 |
| Two-stage: Stage 2 | 23 | 0.96 | 0.94 |
| Direct 4-class model | 44 | 0.84 | 0.82 |

The test sets are small (fewer than 50 tweets), so a single misclassification shifts the scores by
several percentage points. Treat these results as indicative.

## Getting started

The notebooks were developed on Google Colab. A GPU is recommended but not required.

**Colab:** open a notebook, upload `data/dataset.xlsx`, and set `DATA_PATH` in the data-loading
cell to the uploaded file's location.

**Locally:**

```bash
git clone https://github.com/Syukrondzeko/cyberbullying-detection.git
cd cyberbullying-detection
pip install -r requirements.txt
jupyter notebook notebooks/two_stage_indobert.ipynb
```

### Hyperparameters (both stages)

| Parameter | Value |
|---|---|
| Base model | `indolem/indobertweet-base-uncased` |
| Max sequence length | 128 |
| Learning rate | 2e-5 |
| Batch size | 16 |
| Epochs | 4 (best checkpoint by validation loss) |
| Weight decay | 0.01 |
| Seed | 42 |

## Citation

```bibtex
@article{syukron2026hierarchical,
  title   = {A Hierarchical Two Stage BERT Model for Cyberbullying Detection},
  author  = {Syukron, Muhamad and Ramadani, Nayya Safitri and Firmansyah, Muhammad Ilham and Putri, Asi Emilia},
  journal = {IJNMT (International Journal of New Media Technology)},
  volume  = {13},
  number  = {1},
  pages   = {57--64},
  year    = {2026},
  doi     = {10.31937/ijnmt.v13i1.4730}
}
```

## License

The code is released under the [MIT License](LICENSE). The tweet texts remain subject to X (Twitter)'s terms
of service. Use them for research purposes only.
