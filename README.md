# Tetouan Electricity Consumption Analysis

An end-to-end data analysis project on 10-minute electricity consumption
readings across 3 power distribution zones in Tetouan, Morocco (2017),
cross-referenced with temperature, humidity and wind speed. Built with
Python (pandas) for the analysis and a hand-built, no-framework HTML/CSS/
JavaScript dashboard for the presentation layer.

**[Live dashboard →](https://YOUR-USERNAME.github.io/electricity-consumption-morocco/)**
*(replace `YOUR-USERNAME` once this repo is pushed and GitHub Pages is enabled — see [Deploying the live demo](#deploying-the-live-demo))*

![Dashboard preview](docs/screenshot.png)
*(add a screenshot of `docs/index.html` here — see [Adding a screenshot](#adding-a-screenshot))*

## Project goal

Given a raw 52,416-row time series, answer three questions with evidence,
not intuition:

1. What are the hourly, weekly and yearly seasonal patterns in consumption?
2. Do temperature, humidity and wind speed correlate with consumption, and
   which correlates most strongly?
3. Do the three distribution zones behave differently?

## Key findings

| Question | Answer |
|---|---|
| Daily pattern | Trough at 6–7 AM (~50k kWh), peak at **8 PM** (~98k kWh) |
| Weekly pattern | Flat Mon–Sat, **~5% lower on Sundays** |
| Yearly pattern | Peak in **July/August** (summer), lowest in Nov/Dec |
| Strongest weather correlate | **Temperature** (r ≈ 0.49), ahead of humidity (r ≈ −0.30) and wind speed (r ≈ 0.22) |
| Dominant zone | **Zone 1** — 45.4% of total consumption, also the most stable |
| Most seasonal zone | **Zone 3** — swings ~2.5× between December (~11.0k kWh) and July (~28.2k kWh), pointing to a heavy summer cooling load |
| Zone correlation | Zones 1↔2 move closest together (r = 0.835); Zone 3 diverges most (r = 0.571 with Zone 2) |

Full breakdown, methodology and all supporting numbers are in the
[interactive dashboard](docs/index.html) and in [`analysis/analyze.py`](analysis/analyze.py).

## Tech stack

- **Analysis**: Python, [pandas](https://pandas.pydata.org/)
- **Dashboard**: vanilla HTML/CSS/JavaScript, [Chart.js](https://www.chartjs.org/) for charts — no build step, no framework
- **BI**: Power BI–ready enriched dataset + DAX measures (for anyone who wants to reproduce the analysis in Power BI Desktop instead)

## Project structure

```
.
├── data/
│   ├── powerconsumption.csv              # raw dataset (Kaggle)
│   ├── powerconsumption_datadictionary.csv
│   └── powerconsumption_enriched.csv     # + Hour/DayName/Season/IsWeekend/TotalConsumption
├── analysis/
│   └── analyze.py                        # pandas script: all aggregates + correlations
├── docs/
│   └── index.html                        # the dashboard (served live via GitHub Pages)
├── powerbi/
│   ├── DAX_measures.txt                  # ready-to-paste Power BI measures
│   └── POWERBI_GUIDE.md                  # step-by-step Power BI Desktop build guide
├── requirements.txt
└── README.md
```

## Running it yourself

**View the dashboard** — open [`docs/index.html`](docs/index.html) directly in
any browser (no server needed), or use the live GitHub Pages link above.

**Reproduce the analysis**:
```bash
pip install -r requirements.txt
python analysis/analyze.py
```
This prints every aggregate (hourly/weekly/monthly/seasonal patterns,
weather correlations, zone comparisons) used to build the dashboard and the
Power BI dataset.

**Build the Power BI report** — see [`powerbi/POWERBI_GUIDE.md`](powerbi/POWERBI_GUIDE.md).

## Dataset

[Tetouan City Power Consumption](https://www.kaggle.com/datasets/fedesoriano/electric-power-consumption) —
52,416 rows, 10-minute resolution, January 1 – December 30, 2017: `Temperature`,
`Humidity`, `WindSpeed`, `GeneralDiffuseFlows`, `DiffuseFlows`, and power
consumption (kWh) for `Zone1`, `Zone2`, `Zone3`.

## What I'd add next

- Backfill missing intermediate days in the raw feed (the source data has a
  couple of short gaps) and re-validate the daily aggregates.
- A simple next-day consumption forecast (e.g. gradient boosting on
  hour/weekday/season/temperature) as a stretch goal beyond descriptive analysis.
- Swap the hand-rolled zone correlation for a proper significance test
  (the current numbers are plain Pearson r over the full year, not
  seasonally adjusted).

## Deploying the live demo

1. Push this repo to GitHub.
2. Repo **Settings → Pages → Source**: deploy from branch `main`, folder `/docs`.
3. GitHub publishes it at `https://YOUR-USERNAME.github.io/REPO-NAME/` within a
   minute or two — update the link at the top of this README once it's live.

## Adding a screenshot

Open `docs/index.html` in a browser, take a screenshot of the dashboard, save
it as `docs/screenshot.png`, and commit it — GitHub renders it inline at the
top of this README automatically.

## License

[MIT](LICENSE)
