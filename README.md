# Nigeria Food Price Inflation — What It Means for Your Pocket

An analysis of 24 years of food prices in Nigerian markets, from 2002 to 2026, using the World Food Programme's
(WFP) market price survey. Each analysis in the notebook ends with a plain-language explanation of what the
result means for an ordinary household.

Everything is in one notebook: [`food_inflation.ipynb`](food_inflation.ipynb).

---

## Key findings

| | Finding |
|---|---|
| 📈 | **Food costs about 4.4× what it did in January 2019.** A ₦20,000 monthly food bill in 2019 is about ₦88,000 today. |
| 🔥 | **2024 was the worst year.** Food prices more than doubled in 12 months, peaking at +112 % year-on-year in June 2024, after the petrol subsidy ended and the naira was unified. |
| 🌾 | **2025 brought relief for grains and beans (−25 %)**, but **meat, fish, eggs and cooking oil are still rising**. |
| 💱 | **The naira drives food prices.** Food inflation follows the exchange rate about one month later (correlation 0.81). Measured in US dollars, food costs about the same as in 2019. |
| 📅 | **Prices follow the harvest calendar.** Grain is cheapest in October–December and dearest in July–August, a 20–25 % swing. |
| 🏭 | **Processed food rose most.** Maize flour rose ×6.4, whole millet grain ×4.5. Gari bought by the cup costs about 50 % more than gari bought by the bag. |
| 🗺️ | **Lagos pays about 30 % more than the North** for the same item, but northern prices are rising faster. |
| 🔮 | **Outlook: about +14 % by August 2026** (80 % range −5 % to +44 %), mostly the normal lean-season rise. |
| 🚨 | **The early-warning model** caught 86 % of 3-month price surges in testing (AUC 0.87), compared with 61 % for a simple rule. About half of its alarms were false. |

These figures come from the latest run of the notebook, on data up to February 2026.

---

## The notebook

Each section is **one code cell followed by one findings cell**:

| # | Analysis | Question it answers |
|---|---|---|
| 1 | Data & coverage | What data do we actually have, and where and when was it collected? |
| 2 | National food price index | How fast are food prices rising? |
| 3 | Food groups | Which kinds of food got more expensive? |
| 4 | Everyday items | What did a crate of eggs, a litre of oil or a bag of rice cost in 2019, and what does it cost now? |
| 5 | States | Where is food most expensive, and where is it rising fastest? |
| 6 | Seasonality | When is the cheapest time to buy, and the best time to sell? |
| 7 | Retail vs wholesale | How much more does buying in small measures cost? |
| 8 | Drivers | How closely do food prices follow the naira and petrol? |
| 9 | Forecast | Where are prices heading in the next 6 months? |
| 10 | Early warning | Can we see a price surge coming, state by state? |

The notebook ends with a summary table of practical advice for households, farmers and government.

---

## Method

The raw data mixes very different pack sizes: 100 kg wholesale sacks (₦10,000–50,000) and 250 g retail sachets
(₦160) appear in the same file. The markets and states surveyed also change over time. A simple average of
`price` therefore measures **which products happened to be surveyed that month**, not inflation.

This notebook avoids that problem by only comparing a price **with itself**:

- **Price index (sections 2, 5, 10).** For each market × product × pack size, take the month-to-month log price
  change. Average those changes across markets, then across products (equal weight), and chain them into an index.
  This "matched sample" approach is the same idea statistics offices use for the CPI.
  - Gaps of up to 3 months are spread evenly over the missing months.
  - Jumps of more than 4.5× are treated as data errors.
  - A single month's change is capped at ±50 %.
- **Price changes (sections 3, 4).** Compare the same item in the same market: the 2019 median price against the
  median over the last 12 months.
- **State price levels (section 5).** Compare each state's price with the median across all states for the
  *same product in the same month*.
- **Seasonality (section 6).** Remove the long-term trend from each series with a centred 2×12 moving average,
  then take the median deviation by calendar month.
- **Forecast (section 9).** Four models are compared: no change, trend, Ridge autoregression and a small neural
  network (MLP). Each is re-trained at 80 monthly origins (2019–2025) using only past data, then scored on what
  actually happened. The prediction interval comes from the chosen model's own back-test errors.
- **Early warning (section 10).** A "surge" is defined as a state's food prices rising more than 10 % over the next
  3 months. Logistic regression and gradient boosting are trained on 2015–2020 and tested on 2021–2025, against a
  simple momentum rule.

---

## Data

| File | Contents |
|---|---|
| `wfp_food_prices_nga.csv` | 56,163 monthly prices, 68 markets, 14 states, 43 foods (67 food × pack-size products), Jan 2002 – Feb 2026 |
| `wfp_markets_nga.csv` | Market list with coordinates (not used yet) |

**Source:** World Food Programme, *Food Prices for Nigeria*, published on the
[Humanitarian Data Exchange (HDX)](https://data.humdata.org/dataset/wfp-food-prices-for-nigeria).

If the local CSV is missing, the notebook downloads it from this repository on GitHub.

---

## Running it

The project requires **Python ≥ 3.13** and uses [uv](https://docs.astral.sh/uv/):

```bash
uv sync                               # install dependencies into .venv
uv run --with jupyter jupyter lab     # open food_inflation.ipynb
```

Then choose **Kernel → Restart & Run All**. A full run takes about a minute.

To also export every chart as a standalone HTML file into `charts/`, set `SAVE_HTML = True` in the first code cell.

**Stack:** DuckDB (SQL), pandas, scikit-learn, Plotly.

---

## Limitations

- **Geographic coverage.** This is WFP humanitarian monitoring focused on the conflict-affected North-East.
  Since February 2023 only Borno and Yobe (and Adamawa until late 2025) are surveyed. Most other states had a single
  market. Recent results describe the North-East, not Lagos or the whole country.
- **Not the official figure.** These results are **not the NBS Consumer Price Index**, which uses a different basket,
  weights and coverage.
- **Thin early data.** Before 2014 there are only a handful of products and markets, so the time series start in 2015.
- **Petrol gap.** Petrol prices were not recorded from February to December 2025.
- **Correlation is not causation.** The naira and petrol results show that prices move together, not that one
  causes the other.
- **Forecast uncertainty.** Forecasts beyond about three months are highly uncertain; see the error table in section 9.
