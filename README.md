# decision-model-datalabel-experiment

A pair of Jupyter notebooks experimenting with the [Laya](https://huggingface.co/convaiinnovations/laya)
open-weight decision model as a first-pass labeller for bioreactor data. The question being explored: can a
zero-shot decision model canonicalize the messy, partially-labelled datastreams that come off a bioreactor
system (here, a raw AMBR250 file dump) well enough to save a human real labelling effort?

This is an experiment, not a library - there is no package to import, no test suite and no release process.
Everything lives in the notebooks.

## What's in here

- **`Decision Model Experimenting.ipynb`** - the playground. Hand-built `pandas` series (pH, temperature,
  stirrate, ...) are pushed through the model to see how the answers move with prompt phrasing, the amount of
  metadata supplied, and the confidence threshold. Start here.
- **`Testing On Real Data.ipynb`** - the real test. Downloads the public
  [ABPDU fermentation bioreactor sample data](https://abpdu.lbl.gov/resources/fermentation-bioreactor-sample-data/),
  asks the model to assign five canonical labels (name, submeasurement, unit, process location, signal type)
  to every datastream from one reactor, then scores the output against hand-labelled ground truth and against
  a "best fixed guess" baseline.
- **`canonical_labels/`** - the ad-hoc ontology used by the second notebook (`canonical_names.csv`,
  `canonical_units.csv`, etc.) plus `correct_labels.csv`, the manually-labelled ground truth it scores against.
- **`results.csv`** - the saved output of the inference run in the second notebook, so you can skip straight
  to the analysis cells without re-running inference.
- **`example_data/`** - the downloaded AMBR250 dataset (~1.8 GB unzipped). Gitignored; the notebook fetches it.

## Running the notebooks

You need Python 3.10+ and [uv](https://docs.astral.sh/uv/), plus network access on the first run.

```bash
# Install uv, if you don't already have it
curl -LsSf https://astral.sh/uv/install.sh | sh

git clone https://github.com/tsdobbs/decision-model-datalabel-experiment.git
cd decision-model-datalabel-experiment

uv sync              # creates .venv from uv.lock
uv run jupyter lab   # or: uv run jupyter notebook
```

Then open either notebook and run the cells top to bottom.

A few things worth knowing before you do:

- The first `Router()` call downloads the Laya checkpoint(s) from Hugging Face - roughly 1.5 GB of weights,
  cached under `~/.cache/huggingface`, so it only happens once.
- The second notebook downloads the AMBR250 dataset from Google Drive (via `gdown`) into `example_data/` and
  unzips it there.
- It runs fine on CPU on an ordinary laptop. Labelling ~340 datastreams across five questions still takes a
  while; if you'd rather not wait, the analysis cells read from `results.csv` instead.

## Contributing

Contributions aren't expected - this is a proof of concept. If you want to discuss the approach, reach out to
tim@labradorconsulting.net (see [CONTRIBUTING.md](CONTRIBUTING.md)).

Licensed under the [MIT License](LICENSE).
