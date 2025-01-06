
# A Complete Guide to Using ScraperAPI with Scrapy

In this guide, we’ll show you how to integrate ScraperAPI with Python’s Scrapy framework. By the end, you’ll know exactly how to set up ScraperAPI with your Scrapy spiders to enhance your web scraping experience.

Using ScraperAPI as your proxy solution is straightforward. Simply send your target URL to ScraperAPI via its API endpoint, Python SDK, or proxy port, and it will handle proxy rotation, headers, retries, ban detection, and CAPTCHA bypassing.

---

**Stop wasting time on proxies and CAPTCHAs! ScraperAPI's simple API handles millions of web scraping requests, so you can focus on the data. Get structured data from Amazon, Google, Walmart, and more. 👉 [Start your free trial today!](https://bit.ly/Scraperapi)**

---

## How to Integrate ScraperAPI with Scrapy

Let’s start by modifying your Scrapy request setup to work seamlessly with ScraperAPI. Below are three easy ways to accomplish this.

### 1. Using the API Endpoint

Send requests directly to ScraperAPI’s API endpoint (`http://api.scraperapi.com/`) by adding a simple function to your code. This function formats the URL for ScraperAPI.

Here’s an example:

```python
import scrapy
from urllib.parse import urlencode

API_KEY = 'YOUR_API_KEY'

def get_scraperapi_url(url):
    payload = {'api_key': API_KEY, 'url': url}
    proxy_url = 'http://api.scraperapi.com/?' + urlencode(payload)
    return proxy_url

class QuotesSpider(scrapy.Spider):
    name = "quotes"

    def start_requests(self):
        urls = [
            'http://quotes.toscrape.com/page/1/',
            'http://quotes.toscrape.com/page/2/',
        ]
        for url in urls:
            yield scrapy.Request(url=get_scraperapi_url(url), callback=self.parse)
```

### 2. Using the ScraperAPI SDK

To simplify integration further, you can use the ScraperAPI Python SDK. Install the SDK via `pip` and initialize it with your API key. The SDK provides a method `client.scrapyGet` to handle your requests effortlessly.

### 3. Using Proxy Mode

You can also use ScraperAPI as a standard proxy by setting the proxy in the `meta` parameter:

```python
http://scraperapi:YOUR_API_KEY@proxy-server.scraperapi.com:8001
```

This is particularly useful if you’re familiar with configuring proxy servers in Scrapy.

> **Note:** Scrapy skips SSL verification by default, so you don’t need to disable SSL verification for ScraperAPI requests.

---

## Leveraging Advanced ScraperAPI Features

ScraperAPI allows you to customize your scraping experience by adding parameters to your requests.

### Rendering JavaScript

Enable JavaScript rendering by including the `render=true` parameter in your API requests. Here’s an example:

```python
import scrapy
from urllib.parse import urlencode

API_KEY = 'YOUR_API_KEY'

def get_scraperapi_url(url):
    payload = {'api_key': API_KEY, 'url': url, 'render': 'true'}
    proxy_url = 'http://api.scraperapi.com/?' + urlencode(payload)
    return proxy_url

class QuotesSpider(scrapy.Spider):
    name = "quotes"

    def start_requests(self):
        urls = [
            'http://quotes.toscrape.com/page/1/',
            'http://quotes.toscrape.com/page/2/',
        ]
        for url in urls:
            yield scrapy.Request(url=get_scraperapi_url(url), callback=self.parse)
```

### Sending Custom Headers

To send custom headers, add the `keep_headers=true` parameter in your ScraperAPI requests. Here’s how to include custom headers:

```python
import scrapy
from urllib.parse import urlencode

API_KEY = 'YOUR_API_KEY'

def get_scraperapi_url(url):
    payload = {'api_key': API_KEY, 'url': url, 'keep_headers': 'true'}
    proxy_url = 'http://api.scraperapi.com/?' + urlencode(payload)
    return proxy_url

class QuotesSpider(scrapy.Spider):
    name = "quotes"

    def start_requests(self):
        urls = [
            'http://quotes.toscrape.com/page/1/',
            'http://quotes.toscrape.com/page/2/',
        ]

        headers = {
            "X-MyHeader": "123"
        }

        for url in urls:
            yield scrapy.Request(url=get_scraperapi_url(url), headers=headers, callback=self.parse)
```

---

## Optimizing Scrapy Settings for ScraperAPI

To maximize ScraperAPI’s capabilities, update your Scrapy `settings.py` file with the following configurations:

### Increase Concurrency

ScraperAPI supports scaling from hundreds to millions of requests per day by increasing concurrency. Set the `CONCURRENT_REQUESTS` setting to match your ScraperAPI plan’s thread limit.

**Disable the following settings:**
- `DOWNLOAD_DELAY`
- `RANDOMIZE_DOWNLOAD_DELAY`

These settings slow down Scrapy’s requests and are unnecessary with ScraperAPI.

### Retry Failed Requests

Set `RETRY_TIMES` in `settings.py` to retry failed requests up to three times. This ensures near 100% success rates as most requests will succeed after retries.

### Disable Obeying Robots.txt

Scrapy’s default behavior of obeying `robots.txt` can interfere with ScraperAPI. Disable it by setting:

```python
ROBOTSTXT_OBEY = False
```

### Recommended Settings

Here’s what your `settings.py` file should include:

```python
CONCURRENT_REQUESTS = <Your Plan’s Thread Limit>
RETRY_TIMES = 3
ROBOTSTXT_OBEY = False
DOWNLOAD_DELAY = 0
RANDOMIZE_DOWNLOAD_DELAY = False
```

---

**Stop wasting time on proxies and CAPTCHAs! ScraperAPI's simple API handles millions of web scraping requests, so you can focus on the data. Get structured data from Amazon, Google, Walmart, and more. 👉 [Start your free trial today!](https://bit.ly/Scraperapi)**

--- 
