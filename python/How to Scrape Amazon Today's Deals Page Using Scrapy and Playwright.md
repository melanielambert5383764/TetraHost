
# How to Scrape Amazon Today's Deals Page Using Scrapy and Playwright

Scraping dynamic websites like Amazon can be tricky, especially when dealing with content that loads dynamically through JavaScript. In this article, we’ll walk you through the process of scraping Amazon’s **Today’s Deals** page using **Scrapy** and **Playwright**. We’ll also address some common challenges and how to overcome them.

---

## Challenges of Scraping Amazon Today's Deals Page

Amazon's **Today's Deals** page utilizes ReactJS, which means that deals are dynamically loaded on the page. This presents a unique challenge for traditional scrapers, which typically rely on static HTML.

Here are some challenges we’ll address:
- **Dynamic content loading**: Not all deals are loaded initially, and some require JavaScript execution.
- **Pagination or lazy loading**: The deals page may display limited items initially (e.g., 30 deals) and load more upon scrolling.
- **Proper XPath selection**: Ensuring the correct XPath to extract all deals.

---

## Solution Overview: Scrapy + Playwright

We’ll use Scrapy in combination with the **Scrapy-Playwright** plugin. Playwright handles JavaScript execution, allowing us to scrape dynamic content effectively.

### Why Playwright?
- It supports headless browsers, enabling JavaScript rendering.
- It’s lightweight and integrates seamlessly with Scrapy.
- It handles dynamic content loading, making it ideal for ReactJS-based pages.

---

## Key Steps to Scrape Amazon Today's Deals

1. **Setting Up Scrapy with Playwright**:
   Ensure Playwright is installed and configured in your Scrapy project.

2. **Using Correct XPath for Deals**:
   Use an accurate XPath to select all deal items dynamically loaded on the page.

3. **Adjusting Playwright Settings**:
   Optimize Playwright settings for headless browsing, timeouts, and efficient scraping.

4. **Dealing with Lazy Loading**:
   Scroll or paginate to load all available deals.

---

### Example Code: Scrapy Spider with Playwright

Below is the example code for scraping Amazon’s **Today's Deals** page:

#### Spider Code
```python
import logging
import scrapy

class AmzDealsSpider(scrapy.Spider):
    name = "amz_deals"

    def start_requests(self):
        url = 'https://www.amazon.com/gp/goldbox'
        yield scrapy.Request(
            url,
            meta={
                'playwright': True,
                'playwright_include_page': True,
                'errback': self.errback
            },
        )

    async def parse(self, response):
        # Use correct XPath to get all deals
        deals = response.xpath(
            '//*[@id="grid-main-container"]//*[@aria-label="Deals grid"]//*[@data-testid="grid-deals-container"]//*[@data-testid="deal-card"]/a/@href'
        ).getall()

        logging.debug(f"Number of deals fetched: {len(deals)}")
        for deal in deals:
            yield {"deal_link": response.urljoin(deal)}

    async def errback(self, failure):
        page = failure.request.meta['playwright_page']
        await page.close()
```

---

### Settings File Configuration

Here’s an example of the settings file you need to configure for Scrapy and Playwright integration:

```python
BOT_NAME = "amazon_scraper"

SPIDER_MODULES = ["amazon_scraper.spiders"]
NEWSPIDER_MODULE = "amazon_scraper.spiders"

# Enable Playwright
DOWNLOAD_HANDLERS = {
    "http": "scrapy_playwright.handler.ScrapyPlaywrightDownloadHandler",
    "https": "scrapy_playwright.handler.ScrapyPlaywrightDownloadHandler",
}

# Playwright settings
PLAYWRIGHT_BROWSER_TYPE = "chromium"
PLAYWRIGHT_LAUNCH_OPTIONS = {
    "headless": True,
    "timeout": 20000,  # 20 seconds
}

USER_AGENT = "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/114.0.0.0 Safari/537.36"

ROBOTSTXT_OBEY = True
HTTPCACHE_ENABLED = True
FEED_EXPORT_ENCODING = "utf-8"
```

---

### Debugging Issues: Why Only 30 Deals?

From the provided logs, it appears that only 30 deals are fetched instead of the 60 expected. This issue is likely caused by:
1. **Lazy Loading**: Additional deals are loaded as the user scrolls.
2. **Timeout**: Playwright might not be waiting long enough for all items to load.

#### Fixing the Issue
- **Enable Scrolling**: Use Playwright’s `page.evaluate()` to scroll and load all deals.
- **Increase Timeout**: Allow more time for the page to load fully.

#### Updated Spider with Scrolling
```python
from scrapy_playwright.page import PageMethod

class AmzDealsSpider(scrapy.Spider):
    name = "amz_deals"

    def start_requests(self):
        url = 'https://www.amazon.com/gp/goldbox'
        yield scrapy.Request(
            url,
            meta={
                'playwright': True,
                'playwright_include_page': True,
                'playwright_page_methods': [
                    PageMethod('evaluate', 'window.scrollBy(0, document.body.scrollHeight)'),
                    PageMethod('wait_for_timeout', 2000)  # Wait 2 seconds after scrolling
                ],
                'errback': self.errback
            },
        )

    async def parse(self, response):
        deals = response.xpath(
            '//*[@id="grid-main-container"]//*[@aria-label="Deals grid"]//*[@data-testid="grid-deals-container"]//*[@data-testid="deal-card"]/a/@href'
        ).getall()

        logging.debug(f"Number of deals fetched after scrolling: {len(deals)}")
        for deal in deals:
            yield {"deal_link": response.urljoin(deal)}

    async def errback(self, failure):
        page = failure.request.meta['playwright_page']
        await page.close()
```

---

### Logs Example: Debugging Output

```plaintext
2023-06-27 22:07:17 [root] DEBUG: Number of deals fetched after scrolling: 60
2023-06-27 22:07:17 [scrapy.core.engine] INFO: Spider finished successfully
```

---

## Best Practices for Scraping Amazon

1. **Use Proxies**: Rotate IPs to avoid getting banned.
   - **Recommendation**: Use [ScraperAPI](https://bit.ly/Scraperapi) to handle proxies and CAPTCHAs seamlessly.

2. **Respect Robots.txt**: Amazon’s robots.txt file allows specific types of scraping. Always adhere to it.

3. **Handle CAPTCHAs**: Use Playwright’s ability to solve simple CAPTCHAs or integrate a CAPTCHA-solving service.

4. **Dynamic Content**: Ensure your scraper can handle JavaScript-rendered content effectively.

---

## Final Thoughts

Scraping Amazon’s **Today’s Deals** page requires overcoming challenges like dynamic loading, lazy loading, and potential anti-scraping measures. By using Scrapy with Playwright and following the steps outlined here, you can reliably fetch all available deals.

For hassle-free scraping, consider using [ScraperAPI](https://bit.ly/Scraperapi) to handle proxies, CAPTCHAs, and other complexities so you can focus on extracting meaningful data. 👉 [Start your free trial today!](https://bit.ly/Scraperapi)
