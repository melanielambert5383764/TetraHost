
# Asynchronous Web Scraping in Python: A Complete Guide

<iframe title="How To Do Asynchronous Web Scraping In Python" src="https://www.youtube.com/embed/vnzFN5FXqRI?feature=oembed"></iframe>

## Learning Outcomes

- Understand the benefits of using `async` + `await` compared to traditional web scraping with the `requests` library.
- Learn how to build an asynchronous web scraper from scratch using `asyncio` and `aiohttp`.
- Practice downloading multiple web pages using Aiohttp + Asyncio and parsing HTML content per URL with BeautifulSoup.

To follow along, ensure you have the following Python packages installed. If you're using a command line, omit the `!` symbol:

```bash
!pip install beautifulsoup4
!pip install requests
!pip install aiohttp
```

### Library Imports

```python
import aiohttp
from bs4 import BeautifulSoup
import pandas as pd
import requests
```

> **Note:** The `nest_asyncio` library is only required for Jupyter Notebook users. If writing the scraper in a Python file, you can skip the following installation and code block:

```bash
!pip install nest-asyncio
```

```python
import nest_asyncio
nest_asyncio.apply()
```

---

## Why Use Asynchronous Web Scraping?

While synchronous web scraping is simpler and less complex, it is significantly slower. This is because each request must wait for the previous one to finish. Only one request can run at a time.

In contrast, asynchronous web requests execute simultaneously, independent of other requests in the queue. This results in significantly faster scraping.

![Asynchronous vs Synchronous](https://website.understandingdata.com/wp-content/uploads/2021/08/image-93.gif)

---

## How Asynchronous Web Scraping Differs From Python Requests

Instead of using a traditional for loop with multiple requests, asynchronous scraping involves creating an event loop. For example, while NodeJS inherently operates on a single-threaded event loop, Python allows us to manually create one using `asyncio`.

Within the event loop, tasks are created and executed asynchronously.

---

## Web Scraping a Single Page with Aiohttp

Here's how you can scrape a single webpage asynchronously using Aiohttp:

```python
import aiohttp
import asyncio

async def main():
    async with aiohttp.ClientSession() as session:
        async with session.get('http://python.org') as response:
            print("Status:", response.status)
            print("Content-type:", response.headers['content-type'])

            html = await response.text()
            print("Body:", html[:15], "...")

asyncio.run(main())
```

### Key Points:

1. **Client Session Creation**:
   ```python
   async with aiohttp.ClientSession() as session:
   ```

2. **Making a GET Request**:
   ```python
   async with session.get('http://python.org') as response:
   ```

3. **Awaiting the Response**:
   ```python
   html = await response.text()
   ```

4. **Defining Asynchronous Functions**:
   Each async function starts with:
   ```python
   async def function_name:
   ```

5. **Running the Event Loop**:
   ```python
   asyncio.run(main())
   ```

> **Advertisement**  
> Stop wasting time on proxies and CAPTCHAs! ScraperAPI's simple API handles millions of web scraping requests, so you can focus on the data. Get structured data from Amazon, Google, Walmart, and more. 👉 [Start your free trial today!](https://bit.ly/Scraperapi)

---

## Web Scraping Multiple Pages with Aiohttp

When scraping multiple pages, follow this pattern to create tasks that run simultaneously in an event loop:

```python
tasks = []

for url in urls:
    tasks.append(some_function(session, url))

htmls = await asyncio.gather(*tasks)
```

- **Task Creation**:
   For every URL, attach a function, a session, and the URL itself to the list of tasks.

- **Gathering Results**:
   `asyncio.gather(*tasks)` runs the event loop until all tasks are completed, returning results as a list.

### Full Example:

```python
class WebScraper:
    def __init__(self, urls):
        self.urls = urls
        self.all_data = []
        self.master_dict = {}
        asyncio.run(self.main())

    async def fetch(self, session, url):
        try:
            async with session.get(url) as response:
                text = await response.text()
                return text, url
        except Exception as e:
            print(str(e))

    async def main(self):
        tasks = []
        headers = {"user-agent": "Mozilla/5.0 (compatible; Googlebot/2.1; +http://www.google.com/bot.html)"}
        async with aiohttp.ClientSession(headers=headers) as session:
            for url in self.urls:
                tasks.append(self.fetch(session, url))

            htmls = await asyncio.gather(*tasks)
            self.all_data.extend(htmls)

            for html in htmls:
                if html:
                    url = html[1]
                    self.master_dict[url] = {'Raw Html': html[0]}
```

### Example Usage:

```python
urls = ['https://website.understandingdata.com/', 'http://twitter.com/']

scraper = WebScraper(urls=urls)
print(len(scraper.all_data))
```

---

## Parsing HTML with BeautifulSoup

Besides collecting raw HTML, you can parse web pages for specific elements, such as the `<title>` tag:

```python
class WebScraper:
    def __init__(self, urls):
        self.urls = urls
        self.all_data = []
        self.master_dict = {}
        asyncio.run(self.main())

    async def fetch(self, session, url):
        try:
            async with session.get(url) as response:
                text = await response.text()
                title_tag = await self.extract_title_tag(text)
                return text, url, title_tag
        except Exception as e:
            print(str(e))

    async def extract_title_tag(self, text):
        try:
            soup = BeautifulSoup(text, 'html.parser')
            return soup.title
        except Exception as e:
            print(str(e))

    async def main(self):
        tasks = []
        headers = {"user-agent": "Mozilla/5.0 (compatible; Googlebot/2.1; +http://www.google.com/bot.html)"}
        async with aiohttp.ClientSession(headers=headers) as session:
            for url in self.urls:
                tasks.append(self.fetch(session, url))

            htmls = await asyncio.gather(*tasks)
            self.all_data.extend(htmls)

            for html in htmls:
                if html:
                    url = html[1]
                    self.master_dict[url] = {'Raw Html': html[0], 'Title': html[2]}
```

### Example Usage:

```python
scraper = WebScraper(urls=urls)
print(scraper.master_dict['https://website.understandingdata.com/']['Title'])
```

---

## Conclusion

Asynchronous web scraping is highly efficient for processing large numbers of URLs. It allows for faster data collection and makes it easy to integrate additional features like HTML parsing using BeautifulSoup.

Ready to take your web scraping to the next level? 👉 [Start your free trial with ScraperAPI today!](https://bit.ly/Scraperapi)
