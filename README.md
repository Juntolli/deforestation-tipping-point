# Deforestation Tipping-Point Simulation

I was inspired by an article suggesting that the Amazon rainforest might have a tipping point: a moment where enough deforestation leaves the forest so patchy that it can no longer sustain itself, and it begins to collapse on its own, faster than humans are actually clearing it. I wanted to see whether I could reproduce that kind of behaviour in a simple 2D cellular automaton. This project is that experiment, not a scientific model of the real Amazon, but an attempt to recreate the dynamic and find the tipping point inside it.

The central question: **how much deforestation pushes a forest past the point of no return, and how early must intervention happen to prevent total collapse?**

<img width="1600" height="1008" alt="model_vs_reality" src="https://github.com/user-attachments/assets/52121d15-1f4d-451f-a188-8e85e4d670c3" />


## Result

Across 50 Monte Carlo runs on a 100x100 grid, the forest reaches peak fragmentation around **step 63**, with roughly **170 vulnerable cells** (forested cells that have fewer than five forested neighbours). I treat this peak as the tipping point.

Testing a 100% deforestation ban at different times shows that the timing of the intervention matters more than the intervention itself:

| Ban applied | Outcome |
|---|---|
| Step 68 (after the tipping point) | Forest still collapses |
| Step 58 (5 steps before) | Forest still collapses |
| Step 50 (about 13 steps before) | Forest stabilizes and survives |

The takeaway: once the forest passes peak fragmentation, stopping deforestation is no longer enough. The remaining patches are too disconnected to recover, and the collapse continues on its own. Effective intervention has to happen well before the system looks critical.

<img width="1200" height="881" alt="tipping_point_curve" src="https://github.com/user-attachments/assets/f8501421-5762-44b2-9bf0-70cbb503bf88" />

## How the model works

Each cell on the grid is one of three states: **Forested**, **Deforested**, or **Dead** (collapsed). The grid evolves one step at a time under three rules:

1. **Random deforestation**: each forested cell has a small independent probability of being cleared, representing scattered human activity.
2. **Clustered deforestation**: forested cells next to already-deforested cells are more likely to be cleared, reflecting how roads, agriculture, and logging spread outward from existing clearings rather than appearing randomly.
3. **Connectivity collapse**: a forested cell with fewer than five forested neighbours dies, modelling ecological fragmentation, where isolated patches can no longer sustain a stable ecosystem.

Rules 1 and 2 are the human-driven loss; rule 3 is the self-sustaining collapse that takes over once the forest is fragmented enough.

## Method

- Neighbour counting for both the clustering and connectivity rules is done with `scipy.signal.correlate2d` against a 3x3 kernel, with wrap-around boundaries.
- Forest fragility is tracked with a custom metric: the count of "vulnerable" forested cells (those with fewer than five forested neighbours), measured at every step.
- The tipping point is estimated by running the simulation 50 times and averaging the step at which the vulnerable-cell count peaks.
- Intervention is evaluated quantitatively: a run is counted as collapsed if forest cover drops below 25%, and each ban timing is tested across 50 runs to get a survival rate rather than a single anecdotal outcome.

## Tech

Python, NumPy, SciPy (`correlate2d`), Matplotlib (plotting and animation), Jupyter / Google Colab

## Running it

Open `Deforestation_Project.ipynb` in Jupyter or Colab and run all cells top to bottom. The animation cells render the grid evolving in real time; the analysis cells reproduce the tipping-point estimate and the intervention comparison.

## Goal and limitations

The goal of this project was to test my own skills: to see whether I could visually reproduce the look of real deforestation while using programming and data to understand the *why* behind it, the behaviour that drives a forest past the point of recovery. 

- **It is an analogy, not a prediction.** The model is not fitted to real deforestation data, and its steps do not correspond to real units of time. It reproduces the *pattern* and *dynamic* of fragmentation, not the actual Amazon.
- **The rules are simplified.** Deforestation is driven by a fixed probability and a fixed clustering chance, and collapse is governed by a single connectivity threshold (fewer than five forested neighbours). Real ecosystems involve far more factors (soil, climate feedback, species, regrowth) that the model ignores. In particular, there is no regrowth: a cell never recovers once lost.
- **The tipping point is an observed pattern, not a proven threshold.** I defined it as the peak in "vulnerable" cells and supported it with repeated trials, but it is an empirical observation from this model, not a formally derived critical value.
---

*Built as a computational modelling project for COMP 215 (Capilano University).*
