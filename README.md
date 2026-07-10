# GT-MVT-Monthly-City

Google Trends scrapers that collect monthly search interest in motor vehicle theft (MVT) terms for major U.S. metropolitan areas, January 2015 through December 2022. The repository is part of a dissertation project that evaluates Google Trends search data as a proxy for crime reporting.

## What it does

The scrapers query Google Trends for the composite keyword:

```
car stolen+find stolen car+report police stolen car+insurance car stolen-dream-check
```

Google Trends does not report data for cities directly at this granularity, so each city is represented by its metro (DMA) market, for example `US-NY-501` for New York NY or `US-LA-622` for New Orleans LA. The notebooks cover 46 metro areas selected to contain the most populous U.S. cities (the full code-to-name mapping sits in the `Geo Location and Geo Codes` section of `GT_Scrapper_Monthly_City.ipynb`).

Because Google Trends normalizes each response from a fresh sample of searches, a single pull is noisy. The scrapers therefore repeat the 2015-2022 pull for every metro area up to 100 times, saving each run as its own timestamped CSV. Averaging across these repeated samples yields more stable estimates, and all runs are kept in this repository for reproducibility.

## Notebooks

- `GT_Scrapper_Monthly_City.ipynb`: main scraper. Uses the `pytrends` API (`interest_over_time`) with custom request headers, loops over the 46 metro areas, and writes one CSV per area per run into `csv_files/`. Includes a helper that rescales overlapping windows by their mean ratio when a series is assembled from more than one request.
- `GT_Scrapper_Monthly_City-Selenium.ipynb`: alternative scraper that drives the Google Trends website directly with Selenium (entering the keyword, setting a custom date range, switching the map to Metro, and clicking the CSV export button). It then cleans the downloaded `multiTimeline.csv` and renames it into the same `csv_files/` naming scheme. Useful when the API route is rate-limited; the committed configuration targets Cincinnati OH and New Orleans LA.
- `R GT scraping method.ipynb`: minimal sketch of the same task in R with the `gtrendsR` package.
- `test_time_series_modeling.ipynb`: scratch notebook for time-series diagnostics (seasonal decomposition, ACF/PACF, augmented Dickey-Fuller tests) using the statsmodels CO2 example dataset, plus loading of ICPSR crime data files from a local path.

## Data

`csv_files/` holds the raw output of each scrape run (10,764 files collected between April 2023 and April 2024). File names follow the pattern:

```
<Metro area name>_monthly_2015-01-01_2022-12-31_YYYYMMDD_HH_MM.csv
```

where the trailing timestamp records when the run finished. Each file contains a monthly date index and one column, `MVT_GT_<Metro area name>`, holding the Google Trends relative search-interest index (0-100 within each query). Months with zero (low-volume) values are dropped. `csv_files/multiTimeline.csv` is one cleaned Selenium-route download for New Orleans LA that kept the default export name.

## Repository structure

```
GT_Scrapper_Monthly_City.ipynb            Main pytrends scraper
GT_Scrapper_Monthly_City-Selenium.ipynb   Browser-automation scraper (CSV export route)
R GT scraping method.ipynb                gtrendsR sketch
test_time_series_modeling.ipynb           Time-series diagnostics scratch notebook
csv_files/                                Timestamped CSV output, one file per area per run
LICENSE                                   MIT license
README.md                                 This file
```

## Requirements

- Python 3 with `pandas`, `numpy`, `scipy`, `pytrends`, `selenium`, `requests`, `statsmodels` (for the modeling notebook)
- A browser driver for the Selenium routes. The pytrends notebook expects Microsoft Edge with `msedgedriver.exe`; the Selenium notebook uses Chrome through `webdriver_manager`. Driver binaries are not included in this repository; download the version that matches your browser and place it next to the notebooks (or add it to your PATH).
- R with `gtrendsR` for the R notebook.

## How to run

1. Install the requirements and download the browser driver.
2. For the API route, open `GT_Scrapper_Monthly_City.ipynb`, update the request headers with your own current Google Trends cookie (captured from a logged-in browser session; the committed values are stale), adjust the metro dictionary and the `From`/`To` dates if needed, then run the cells in order. Update the output path in the execution loop to your local `csv_files/` location.
3. For the browser route, open `GT_Scrapper_Monthly_City-Selenium.ipynb`, set the metro dictionary, keyword, and date range in the Execution section, and update the download and output directories in `read_and_rename_csv_file`.

Both routes sleep for long, randomized intervals between requests (roughly ten minutes between areas on the API route, about three hours between full passes) to stay within Google Trends rate limits, so a full run takes weeks. On repeated rate-limit errors the API route backs off for about 24 hours before retrying.

## Related repositories

- [GT-MVT-Annually-State](https://github.com/Yu-Hsuan-Liu/GT-MVT-Annually-State): annual scrape at the state level
- [GT-MVT-Annually-DMA](https://github.com/Yu-Hsuan-Liu/GT-MVT-Annually-DMA): annual scrape at the DMA level

## License

MIT. See `LICENSE`.
