# Harmonica Timbre Analysis


## overview

This repository contains the code and datasets used in the work:

> <strong>Spectral centroid and attack time as timbre descriptors of the diatonic harmonica: quantifying the acoustic signature of the bending technique</strong>

It provides the audio recordings, extracted acoustic descriptors, and the full analysis pipeline (Python for signal processing, R for statistical testing) supporting a controlled crossover design that examines the effects of playing technique (natural vs. bending) and comb material (wood vs. ABS plastic) on the harmonica's timbre.

## repository structure

```
harmonica-timbre-analysis/
├── data/
│   ├── audios_v1/              # audio recordings from initial pilot study
│   ├── audios_v2/
│   ├── descriptors_v1.csv
│   ├── descriptors_v2.csv
│   └── rew_measurements/
│       ├── *.mdat
│       └── *.txt
│       
├── notebooks/
│   ├── recording_randomization.ipynb
│   ├── audio_processing.ipynb
│   ├── data_visualization.ipynb
│   └── permanova.ipynb
├── requirements.txt
└── README.md
```

## recording setup

`data/rew_measurements/`: Measurements for room acoustic characterization (reverberation time and noise floor) performed using [Room EQ Wizard (REW)](https://www.roomeqwizard.com/).

## audio data

`data/audios_v2/`: Folder with audio recordings of the isolated notes.

`data/descriptors_v2.csv`: Dataset containing spectral centroid and attack time values for each repetition (generated after running `notebooks/audio_processing.ipynb`).

## notebooks

### Python Notebooks

`recording_randomization.ipynb`: Generates the randomized order of combinations used during recording sessions.

`audio_processing.ipynb`: Loads the audio files, computes the STFT, timbre descriptors, spectrograms, and builds the dataset.

`data_visualization.ipynb`: Generates note-by-note boxplots and computes medians and IQR.

#### How to use (Linux):
```bash
python -m venv .venv
source .venv/bin/activate  
pip install -r requirements.txt
```
(Essentia is not available via `pip` for Windows.)

### R notebook

`permanova.ipynb`: Runs the PERMANOVA and PERMDISP tests, along with post-hoc tests.

#### How to use
```r
install.packages("remotes")
remotes::install_github("pmartinezarbizu/pairwiseAdonis/pairwiseAdonis")

install.packages("vegan")
install.packages("tidyverse")
install.packages("emmeans")
```