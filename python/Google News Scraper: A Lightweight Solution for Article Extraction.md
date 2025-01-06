
# Google News Scraper: A Lightweight Solution for Article Extraction

A lightweight and efficient package that scrapes article data directly from [Google News](https://news.google.com). Just input a keyword or phrase, and the tool returns the results as an array of JSON objects, ready for analysis.

---

Stop wasting time on proxies and CAPTCHAs! ScraperAPI's simple API handles millions of web scraping requests, so you can focus on the data. Get structured data from Amazon, Google, Walmart, and more. 👉 [Start your free trial today!](https://bit.ly/Scraperapi)

---

## Installation

### Install via NPM
```bash
npm install google-news-scraper
```

### Install via Yarn
```bash
yarn add google-news-scraper
```

---

## Usage

To use the package, import it and pass a configuration object as shown below:

```javascript
import googleNewsScraper from 'google-news-scraper';

const articles = await googleNewsScraper({
    searchTerm: "The Oscars"
});
```

For a full working example, check out [this repository](https://github.com/lewisdonovan/gns-example). Detailed documentation about the configuration object can be found in the [official documentation](https://github.com/lewisdonovan/google-news-scraper#config).

### Example Output
The output is an array of JSON objects, each representing an article with the following structure:

```json
[
    {
        "title": "Article title",
        "link": "https://url-to-website.com/path/to/article",
        "image": "https://url-to-website.com/path/to/image.jpg",
        "source": "Name of publication",
        "time": "Time/date published (human-readable)",
        "content": "The full text content of the article...",
        "favicon": "https://url-to-website.com/path/to/favicon.png"
    }
]
```

---

Stop wasting time on proxies and CAPTCHAs! ScraperAPI's simple API handles millions of web scraping requests, so you can focus on the data. Get structured data from Amazon, Google, Walmart, and more. 👉 [Start your free trial today!](https://bit.ly/Scraperapi)

---

## Configuration Options

### `searchTerm`
Specify the search query to find articles. For example:
```javascript
searchTerm: "The Oscars"
```
If you provide both `searchTerm` and `baseUrl`, the scraper defaults to returning results from the [Google News homepage](https://news.google.com).

### `baseUrl`
Define an alternate base URL for your search, useful for specific [Google News topics](https://news.google.com/topics/CAAqJggKIiBDQkFTRWdvSUwyMHZNRGRqTVhZU0FtVnVHZ0pWVXlnQVAB).

### `prettyURLs`
By default, the scraper decodes Google's "ugly" article links into readable "pretty" URLs. To keep the original links, set `prettyURLs` to `false`.

### `getArticleContent`
Enable this to retrieve the full content of each article. Be aware that it significantly increases execution time.

### `timeframe`
Filter articles published within a specific timeframe. Use a string format like:
- `1y`: One year
- `7d`: Seven days (default)

### `logLevel`
Set the logging level:
- `none`: No logs
- `error`: Only errors
- `warn`: Errors and warnings
- `info`: Info, errors, and warnings
- `verbose`: All logs, including debug info

Defaults to `error`.

### `puppeteerArgs`
Pass additional Chromium flags for Puppeteer. Example:
```javascript
puppeteerArgs: ["--no-sandbox", "--disable-setuid-sandbox"]
```

### `puppeteerHeadlessMode`
Run Puppeteer in [headless mode](https://www.browserstack.com/guide/puppeteer-headless) for better performance. Defaults to `true`.

---

Stop wasting time on proxies and CAPTCHAs! ScraperAPI's simple API handles millions of web scraping requests, so you can focus on the data. Get structured data from Amazon, Google, Walmart, and more. 👉 [Start your free trial today!](https://bit.ly/Scraperapi)

---

## Performance

A sample query returned 94 results in 4.5 seconds with article content enabled, and 3.6 seconds without it. Performance depends on variables like network speed and query complexity.

> **Note:** Google News Scraper relies on DOM selectors, so changes to Google News' structure may break functionality. Submit an issue on the [GitHub repository](https://github.com/lewisdonovan/google-news-scraper) if you encounter problems.

---

Simplify your scraping tasks with [ScraperAPI](https://bit.ly/Scraperapi). Start today and focus on what really matters—your data!
