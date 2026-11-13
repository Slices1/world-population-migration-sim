# World Population and Migration Simulator

A stochastic, age-structured model of world population and country-to-country migration, fitted to UN data, with an interactive map and country view in the browser.

Modelling and simulation coursework.

**Live app:** https://slices1.github.io/world-population-migration-sim/

## Layout

```
model/                       Written by the team: everything assessed.
  data/                      Downloaded input data (UN WPP, migration flows, country shapes).
  fit_models.ipynb           Loads the data, fits all parameters, fitting figures.
  verify_models.ipynb        Hold-out test, solver checks, comparison with the UN.
  population_simulator.py    The simulator: ODEs, RK4, randomness, ensemble runs.
  data.npz                   Cleaned observed data, written by fit_models.
  params.npz                 Parameters fitted on all data.
  params_fit_to_2010.npz     Parameters fitted on data up to 2010, for the hold-out test.
app/                         Browser visualisation. AI-generated; contains no model logic.
.github/workflows/pages.yml  Publishes app/ plus the simulator to GitHub Pages.
```

The app runs `population_simulator.py` unchanged in the browser, so every number it shows comes from the team's model.

## The model

- **Population:** an age-structured model (the McKendrick–von Foerster equation, discretised into one ODE per single-year age group), solved with a hand-written fourth-order Runge–Kutta stepper.
- **Randomness:** mortality follows a Lee–Carter model whose index is a random walk with drift; fertility follows a mean-reverting process; migration flows are drawn from a Poisson distribution. Each projection is a Monte Carlo ensemble shown as a median with uncertainty bands.
- **Migration:** a gravity model, where the flow between two countries depends on their populations and the distance between them.

## Viewing the results

Open the live app linked above. The map shows each country's projected population year by year, with arcs for migration. Click a country for its population over time, its age pyramid, and sliders to change its assumptions.

## Reproducing the analysis

Only needed to refit the parameters or regenerate the report figures. Requires Python 3.10 or newer.


Download the datasets into `model/data/`. They total about 700 MB and are git-ignored.

**Migration flows** from [Annual migration flow estimates by sex between all countries since 1960](https://data.humdata.org/dataset/annual-migration-flow-estimates-by-sex-between-all-countries-since-1960) (Humanitarian Data Exchange). Download `annual_flow_estimates_v1.0.csv` and rename it to `migration_flows_annual_by_sex_1960-2024.csv`. It holds people moving from each origin to each destination country, per year and sex.

**UN World Population Prospects 2024** from the [CSV downloads page](https://population.un.org/wpp/downloads?folder=Standard%20Projections&group=CSV%20format). Keep the file names as downloaded:

| Section on the page | Link | File |
|---|---|---|
| Population → Population on 01 January, by single age | 1950-2023 (gz) | `WPP2024_Population1JanuaryBySingleAgeSex_Medium_1950-2023.csv.gz` |
| Population → Population on 01 January, by single age | 2024-2100 (gz) | `WPP2024_Population1JanuaryBySingleAgeSex_Medium_2024-2100.csv.gz` |
| Mortality → Deaths, by single age | 1950-2023 (gz) | `WPP2024_DeathsBySingleAgeSex_Medium_1950-2023.csv.gz` |
| Mortality → Deaths, by single age | 2024-2100 (gz) | `WPP2024_DeathsBySingleAgeSex_Medium_2024-2100.csv.gz` |
| Fertility | 1950-2100, single age (gz) | `WPP2024_Fertility_by_Age1.csv.gz` |
| Demographic Indicators | 1950-2100, medium (gz) | `WPP2024_Demographic_Indicators_Medium.csv.gz` |


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

Start Jupyter:

```bash
jupyter notebook
```

Then run the two notebooks in `model/`, top to bottom:

1. **`fit_models.ipynb`** loads the raw data, fits the mortality model and fertility process to UN World Population Prospects data, and fits the gravity model to the migration flows. Run it twice: with `CUTOFF_YEAR = 2010` to write `params_fit_to_2010.npz`, then with `CUTOFF_YEAR = 2023` to write `params.npz`. It also saves the cleaned data to `data.npz`.
2. **`verify_models.ipynb`** checks the solver against smaller time steps, projects 2010–2023 from the 2010 parameters and compares with what actually happened, and compares the long-run projection with the UN's.

The app reads `params.npz`.

To leave the virtual environment:

```bash
deactivate
```
