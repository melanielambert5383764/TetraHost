
# Efficient Strategies for Scraping Thousands of Pages from a Website

Scraping large numbers of web pages can be challenging, especially when dealing with over 20,000 pages. Below is an example of Python code used for web scraping with Selenium, along with suggestions to optimize the process for faster results and better efficiency.

---

## The Problem: Slow Scraping with Selenium

The provided code demonstrates how to scrape multiple pages using Selenium. While it is functional, it can only scrape one page at a time, making it extremely slow for scraping thousands of pages. Here's the current approach:

```python
import time
import random
from selenium import webdriver
from selenium.webdriver.chrome.options import Options
from selenium.webdriver.common.by import By
from selenium.common.exceptions import WebDriverException, NoSuchElementException

user_agents = [
    "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/109.0.0.0 Safari/537.36",
    "Mozilla/5.0 (Macintosh; Intel Mac OS X 13_1) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/16.1 Safari/605.1.15"
]

driver_path = '/users/tosh/downloads/chromedriver'
user_agent = random.choice(user_agents)
chrome_options = Options()
chrome_options.add_argument(f"user-agent={user_agent}")
driver = webdriver.Chrome(executable_path=driver_path, options=chrome_options)

main_url = 'https://sekolah.data.kemdikbud.go.id/index.php/Chome/pencarian/'
driver.get(main_url)
time.sleep(5)

search_button = driver.find_element(By.XPATH, '//*[@id="page-top"]/div[1]/div/div[2]/div[2]/form/div/div/div[3]/div/button')
search_button.click()
time.sleep(5)

max_retries = 3
href_set = set()
page = 1

while True:
    for _ in range(max_retries + 1):
        try:
            section = driver.find_element_by_css_selector('div[id="pageload"]')
            elements = section.find_elements_by_xpath('//li[@class="list-group-item"]/a[contains(., "Lihat")]')
            if not elements:
                break  # No more "Lihat" links, indicating end of data

            for element in elements:
                href = element.get_attribute('href')
                href_set.add(href)

            href_list = list(href_set)
            href_list = [href.replace('/Chome/', '/chome/') for href in href_list]
            with open('output_hrefs.txt', 'w') as file:
                for href in href_list:
                    file.write(href + '\n')

            next_page_link = driver.find_element_by_xpath(f'//ul[@class="pagination pull-left"]/li/a[text()="{page + 1}"]')
            next_page_link.click()
            time.sleep(5)

            page += 1
            break
        except (WebDriverException, NoSuchElementException):
            if _ < max_retries:
                print(f"Retrying page {page}, Attempt {_ + 1}")
                continue
            else:
                print(f"Unable to navigate to page {page} after {max_retries} attempts")
                break

href_list = list(href_set)
href_list = [href.replace('/Chome/', '/chome/') for href in href_list]
with open('output_hrefs.txt', 'w') as file:
    for href in href_list:
        file.write(href + '\n')

driver.quit()
```

---

## Key Challenges with the Above Code

1. **Performance**: Scraping sequentially page by page is highly time-consuming.
2. **Retries**: Retries for each page, though useful, add overhead and further slow down the process.
3. **Data Writing**: Writing to files multiple times introduces inefficiency.

---

## Recommended Solution: ScraperAPI

Stop wasting time managing proxies and dealing with CAPTCHAs! With [ScraperAPI](https://bit.ly/Scraperapi), you can handle millions of web scraping requests effortlessly, allowing you to focus on extracting data from Amazon, Google, Walmart, and more.

👉 **[Start your free trial today!](https://bit.ly/Scraperapi)**

---

## Optimizing Web Scraping: Practical Tips

To improve performance, consider the following strategies:

### 1. Parallel Requests
Use a library like `multiprocessing` or `concurrent.futures` in Python to send requests concurrently. This approach drastically reduces scraping time compared to Selenium’s single-threaded execution.

### 2. ScraperAPI Integration
Switching to ScraperAPI simplifies scraping significantly. Here's an example of how you can integrate ScraperAPI into your Python code:

```python
import requests

api_url = "http://api.scraperapi.com"
api_key = "SCRAPE9837861"
url = "https://sekolah.data.kemdikbud.go.id/index.php/Chome/pencarian/"

params = {
    'api_key': api_key,
    'url': url
}

response = requests.get(api_url, params=params)
if response.status_code == 200:
    print("Scraping successful!")
    print(response.text)
else:
    print("Error:", response.status_code)
```

### 3. Switch to Requests and BeautifulSoup
For static websites or parts of a dynamic site that can be accessed directly, consider using `requests` and `BeautifulSoup`. They are much faster than Selenium for simple scraping tasks.

### 4. Scraping in Batches
Divide the total pages into smaller batches and scrape them in parallel. This reduces the risk of server bans and speeds up the process.

---

## Final Thoughts

Optimizing your scraping workflow can save you time and resources. While Selenium is great for handling JavaScript-heavy websites, combining it with tools like ScraperAPI or switching to more efficient libraries can make a huge difference.

👉 [Start scraping smarter with ScraperAPI today!](https://bit.ly/Scraperapi)
