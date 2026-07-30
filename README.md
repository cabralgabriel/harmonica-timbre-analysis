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
│   ├── audios_v2/              # confirmatory collection, 48 kHz / 24 bit / mono
│   ├── descriptors_v1.csv
│   ├── descriptors_v2.csv
│   └── rew_measurements/
│       ├── *.mdat              # reverberation time, left / right / left+right
│       └── *.txt               # noise floor, five consecutive measurements
│
├── notebooks/
│   ├── recording_randomization.ipynb
│   ├── audio_processing.ipynb
│   ├── data_visualization.ipynb
│   ├── permanova.ipynb
│   ├── harmonic_content_analysis.ipynb
│   ├── level_and_structure_controls.ipynb
│   └── design_robustness.ipynb
├── requirements.txt
└── README.md
```

## experimental design

Two harmonicas with interchangeable reed plates, one in C with a wooden comb and one in B flat with
an ABS plastic comb, were crossed over four notes (E4, F4, G4, A4) and two emission methods, with
five repetitions per cell, giving 80 recordings.

Because the Richter layout does not allow a given pitch to be produced both naturally and by bending
on the same reed, each note is natural on one instrument and bent on the other:

| Note | Harmonica | Hole | Reed | Production |
|---|---|---|---|---|
| E4 | C | 2 | blow | natural |
| E4 | B flat | 2 | draw | bend, one semitone |
| F4 | C | 2 | draw | bend, two semitones |
| F4 | B flat | 2 | draw | natural |
| G4 | C | 3 | blow | natural |
| G4 | B flat | 3 | draw | bend, two semitones |
| A4 | C | 3 | draw | bend, two semitones |
| A4 | B flat | 3 | draw | natural |

Recording proceeded in two blocks, with the order fully randomized within each. Block A used the
factory pairings, C with wood and B flat with ABS; the plates were then swapped and block B used C
with ABS and B flat with wood.

The block is not stored as a column, but it is recoverable from `harp` and `material`, and it is
exactly the harmonica by material interaction. One consequence is worth keeping in mind for any model
fitted on this data: the comb main effect is balanced across blocks, twenty recordings of each comb
in each block, and is therefore estimable; the harmonica by material interaction is perfectly aliased
with the block and is not.

## recording setup

`data/rew_measurements/`: Measurements for room acoustic characterization (reverberation time and noise floor) performed using [Room EQ Wizard (REW)](https://www.roomeqwizard.com/), with the dbx RTA-M calibration curve loaded.

## audio data

`data/audios_v2/`: Folder with audio recordings of the isolated notes. Files follow the pattern
`harp<key>_<material>_<method>_<note>_rep<n>.wav`, for example `harpBb_abs_bend_E4_rep1.wav`.

`data/descriptors_v2.csv`: Dataset containing spectral centroid and attack time values for each repetition (generated after running `notebooks/audio_processing.ipynb`). Columns:

| Column | Meaning |
|---|---|
| `harp` | instrument key, `C` or `Bb` |
| `note` | target pitch, `E4` to `A4` |
| `f0` | measured fundamental frequency, in Hz |
| `material` | comb material, `wood` or `abs` |
| `method` | emission method, `natural` or `bend` |
| `rep` | repetition index within the cell, 1 to 5 |
| `centroid` | spectral centroid in Hz, magnitude weighted, averaged over the 2 to 5 s window |
| `logattacktime` | log attack time, in log10 seconds |
| `atk_time` | attack time in seconds, the linear equivalent of the column above |

The `v1` files come from the pilot collection reported in the original undergraduate thesis and are
kept for reference. The analyses supporting the manuscript use `v2` throughout.

## notebooks

Run them in the order below. Everything after `audio_processing.ipynb` reads `descriptors_v2.csv` and
can be run independently, except `harmonic_content_analysis.ipynb`, which also reads the audio.

### Python notebooks

`recording_randomization.ipynb`: Generates the randomized order of combinations used during recording sessions, and defines the two blocks.

`audio_processing.ipynb`: Loads the audio files, computes the STFT, timbre descriptors, spectrograms, and builds the dataset.

`data_visualization.ipynb`: Generates note-by-note boxplots and computes medians and IQR.

`harmonic_content_analysis.ipynb`: Separates harmonic energy from the broadband noise floor, recomputes the centroid over the harmonic component alone, and describes how energy is distributed across frequency bands. Opens with a validation step that reproduces the published centroid from the raw audio, and closes with clipping and noise floor controls. Uses only the standard library and NumPy, because it needs the raw magnitude spectrum and a per-frame floor estimate rather than the descriptor algorithms.

`level_and_structure_controls.ipynb`: Excludes the readings under which the high frequency result would not be attributable to the technique. Bent notes are quieter, so it checks whether level accounts for the difference, comparing the conditions at matched level rather than by extrapolation. It then verifies that the energy above 5 kHz is harmonically structured rather than rattle or a room mode, by contrasting the harmonic bins against the bins midway between them, checks for clipping, and profiles the harmonic amplitudes to distinguish a passive low-pass loading from a change of regime.

`design_robustness.ipynb`: Reports the comb material effect as a bootstrap interval in interpretable units, corrects the within-plate contrast for the pitch difference it carries, checks that contrast against removal of either block, and derives the smallest p-value attainable once the five repetitions are treated as one experimental unit.

#### How to use (Linux):
```bash
python -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
```
(Essentia is not available via `pip` for Windows.)

### R notebook

`permanova.ipynb`: Runs the PERMANOVA and PERMDISP tests, along with post-hoc tests. Descriptors are converted to z scores before the Euclidean distance matrix is assembled, so that the centroid, in hertz, does not dominate the log attack time, in log10 seconds. Multivariate R-squared values are conditional on that scaling choice; the univariate ones are not.

#### How to use
```r
install.packages("remotes")
remotes::install_github("pmartinezarbizu/pairwiseAdonis/pairwiseAdonis")

install.packages("vegan")
install.packages("tidyverse")
install.packages("emmeans")
```

## citing this repository

The manuscript cites this repository in its data availability statement. On acceptance, archive a
tagged release and replace the plain URL there with the resulting DOI, so that the version supporting
the published results is the one a reader retrieves.
