# World Population and Migration Simulator

A stochastic, age-structured model of world population and country-to-country migration, fitted to UN data, with an interactive map and country view in the browser.

Modelling and simulation coursework.

**Live app:** https://slices1.github.io/world-population-migration-sim/

## Layout

```
model/                       Written by the team: everything assessed.
  data/                      Cleaned input data (UN WPP, migration flows, covariates).
  analysis.ipynb             Parameter fitting, validation and report figures.
  population_simulator.py    The simulator: ODEs, stochastic terms, ensemble runs.
  params.npz                 Fitted parameters, written by the notebook.
app/                         Browser visualisation. AI-generated; contains no model logic.
.github/workflows/pages.yml  Publishes app/ plus the simulator to GitHub Pages.
```

The app runs `population_simulator.py` unchanged in the browser, so every number it shows comes from the team's model.

## The model

- **Population:** an age-structured model (the McKendrick–von Foerster equation, discretised into one ODE per single-year age group), solved with a hand-written fourth-order Runge–Kutta stepper.
- **Randomness:** mortality follows a Lee–Carter model whose index is a random walk with drift; fertility follows a mean-reverting process; migration flows are drawn from a Poisson distribution. Each projection is a Monte Carlo ensemble shown as a median with uncertainty bands.
- **Migration:** a gravity model, where the flow between two countries depends on their populations, the distance between them, and differences in income and safety.

## Viewing the results

Open the live app linked above. The map shows each country's projected population year by year, with arcs for migration. Click a country for its population over time, its age pyramid, and sliders to change its assumptions.

## Reproducing the analysis

Only needed to refit the parameters or regenerate the report figures. Requires Python 3.10 or newer.


Download the datasets:
- [The Bilateral flow estimates (latest version)](https://figshare.com/collections/Bilateral_international_migration_flow_estimates_for_200_countries/4470464) as `bilateral_migration_estimates.csv` 
- [`1950-2023 (gz)` under `Population on 01 January, by single age. Only medium is available` under `Population`](https://population.un.org/wpp/downloads?folder=Standard%20Projections&group=CSV%20format)
- [`2024-2100 (gz)` under `Population on 01 January, by single age. Only medium is available` under `Population`](https://population.un.org/wpp/downloads?folder=Standard%20Projections&group=CSV%20format)
- [`1950-2023 (gz)` under `Deaths, by single age. Only medium is available.` under `Mortality`](https://population.un.org/wpp/downloads?folder=Standard%20Projections&group=CSV%20format)
- [`2024-2100 (gz)` under `Deaths, by single age. Only medium is available.` under `Mortality`](https://population.un.org/wpp/downloads?folder=Standard%20Projections&group=CSV%20format)
- [`1950-2100, single age (gz)` under `Fertility`](https://population.un.org/wpp/downloads?folder=Standard%20Projections&group=CSV%20format)
- [`1950-2100, medium (gz)` under `Demographic Indicators`](https://population.un.org/wpp/downloads?folder=Standard%20Projections&group=CSV%20format)



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

Start Jupyter and open `model/analysis.ipynb`:

```bash
jupyter notebook
```

Run the cells top to bottom. The notebook has three sections:

1. **Births and deaths:** fits the mortality model and the fertility process to UN World Population Prospects data, per country and age.
2. **Migration:** fits the gravity model to estimated country-to-country flows.
3. **Simulation and validation:** fits on data up to 2010, projects 2010–2023, and checks how often the real values fall inside the predicted uncertainty bands. Also checks the solver's accuracy against smaller time steps.

The fitted parameters are saved to `model/params.npz`, which the app reads.

To leave the virtual environment:

```bash
deactivate
```
