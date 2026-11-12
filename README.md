# World Population and Migration Simulator

A stochastic, age-structured model of world population and country-to-country migration, fitted to UN data, with a browser visualisation.

Modelling and simulation coursework.

## Layout

```
model/    Data, fitting notebooks and the simulator (popsim.py). Written by the team.
app/      Browser visualisation. AI-generated; contains no model logic.
```

## Setup

Requires Python 3.10 or newer.

Create and activate a virtual environment from the repository root:

```bash
python3 -m venv .venv
```

```bash
source .venv/bin/activate
```

On Windows, activate with `.venv\Scripts\activate` instead.

Install the dependencies:

```bash
pip install numpy pandas scipy statsmodels matplotlib jupyter
```

## Running

Start Jupyter and open `model/analysis.ipynb`:

```bash
jupyter notebook
```

Run the cells top to bottom. The sections are, in order: demography fits, migration fit, then simulation and validation.

To leave the virtual environment:

```bash
deactivate
```
