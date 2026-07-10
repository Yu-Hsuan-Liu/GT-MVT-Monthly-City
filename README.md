# GT-MVT-Monthly-City

Google Trends scrapers for monthly search interest in motor vehicle theft terms across 46 U.S. metro areas, 2015-2022. Part of my dissertation work on whether search activity can serve as a proxy for crime reporting.

Google Trends draws a fresh sample on every request, so any single pull is noisy. The scraper repeats the pull for each metro area up to 100 times and saves every run as a timestamped CSV in `csv_files/`. Averaging across runs gives a much more stable series. The query is `car stolen+find stolen car+report police stolen car+insurance car stolen-dream-check`.

Two scraping routes:

- `GT_Scrapper_Monthly_City.ipynb` uses the pytrends API. Paste your own Google Trends cookie into the request headers before running; the committed value is a placeholder.
- `GT_Scrapper_Monthly_City-Selenium.ipynb` drives the Google Trends website in a browser and clicks the CSV export instead. Useful when the API route gets rate-limited.

`R GT scraping method.ipynb` is a small gtrendsR sketch of the same idea, and `test_time_series_modeling.ipynb` is a scratch notebook for time-series diagnostics.

Requires pandas, pytrends, and selenium, plus a browser driver (not included). The scrapers sleep for long stretches between requests to stay under Google's rate limits, so a complete run takes weeks.

Related: [GT-MVT-Annually-State](https://github.com/Yu-Hsuan-Liu/GT-MVT-Annually-State) and [GT-MVT-Annually-DMA](https://github.com/Yu-Hsuan-Liu/GT-MVT-Annually-DMA) do the same at annual granularity.
