
# How to Scrape Products from Walmart with PHP, Guzzle, Crawler, and Doctrine

Web scraping is an incredibly useful technique for extracting data from websites, especially when no public API is available, or when a large volume of data needs to be gathered in a more efficient manner. While **cURL** is a common choice for its simplicity and universal compatibility, we'll explore an alternative approach using **Guzzle**, **Symfony DOM Crawler Component**, and **Doctrine DBAL**.

In this guide, we'll walk you through a step-by-step process to scrape product data from [Walmart](http://www.walmart.com), compile it into a CSV file, and store it in a database.

---

## Why Choose ScraperAPI?

Stop wasting time on proxies and CAPTCHAs! ScraperAPI's simple API handles millions of web scraping requests, allowing you to focus on data extraction without the hassle. Scrape structured data from Amazon, Google, Walmart, and more.  
👉 [**Start your free trial today!**](https://bit.ly/Scraperapi)

---

## 1. Install the Required Libraries

To begin, install the necessary libraries using [Composer](https://getcomposer.org/). These libraries will streamline the process and handle everything from HTTP requests to data parsing and database storage.

### 1.1. Guzzle

Install **Guzzle**, a PHP HTTP client, using the command below:

```bash
composer require guzzle/guzzle:~3.9
```

You should see output similar to this:

```text
- Installing guzzle/guzzle (v3.9.3)
  Loading from cache
```

Guzzle may install additional dependencies, such as `symfony/event-dispatcher`. No additional configuration is required.

### 1.2. Symfony DOM Crawler Component

Install **Symfony DOM Crawler Component** for parsing HTML:

```bash
composer require symfony/dom-crawler
```

Once installed, it will recommend adding the **CSS Selector** library:

```bash
composer require symfony/css-selector
```

### 1.3. Doctrine DBAL

For database operations, install **Doctrine DBAL**:

```bash
composer require doctrine/dbal
```

---

## 2. Load the HTML of a Website Page

Create a PHP file, e.g., `scraper.php`, and include the `vendor/autoload.php` file to enable the use of installed libraries:

```php
require 'vendor/autoload.php';
```

### 2.1. Using Guzzle

Set up Guzzle to handle HTTP requests. Include Guzzle's client and exception handling classes:

```php
use Guzzle\Http\Client;
use Guzzle\Http\Exception\ClientErrorResponseException;
```

Define the target URL and User-Agent header to avoid access denial errors:

```php
$url = 'http://www.walmart.com/all-departments';
$userAgent = 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/91.0.4472.124 Safari/537.36';

$client = new Client();
$request = $client->get($url, null, ['headers' => ['User-Agent' => $userAgent]]);
$response = $request->send();
$html = $response->getBody(true);
```

---

## 3. Scrape the Page

### 3.1. Parse HTML with Symfony DOM Crawler

Add the **DOM Crawler Component** to extract elements from the HTML:

```php
use Symfony\Component\DomCrawler\Crawler;

$crawler = new Crawler($html);
```

### 3.2. Extract Categories and Subcategories

Use CSS selectors to isolate the categories and their subcategories. For example:

- Categories: `.all-depts-links-heading > a`
- Subcategories: `li > a.all-depts-links-dept`
- Sub-subcategories: `li > a.all-depts-links-category`

Here's how to loop through and extract this data:

```php
$categories = $crawler->filter('.all-depts-links')->each(function (Crawler $node) {
    return [
        'title' => $node->filter('.all-depts-links-heading > a')->text(),
        'subcategories' => $node->filter('ul > li')->each(function (Crawler $subNode) {
            return [
                'title' => $subNode->filter('a.all-depts-links-dept')->text(),
                'href' => $subNode->filter('a.all-depts-links-dept')->attr('href'),
            ];
        }),
    ];
});
```

---

## 4. Extract Product Data

Subcategories may contain products directly or lead to pages with additional items. Extract product information such as title, price, image, and ratings. Define default values to handle missing fields:

```php
$products = $crawler->filter('ul.tile-list.tile-list-grid > li > div')->each(function (Crawler $node) {
    return [
        'title' => $node->filter('.product-title')->text(),
        'price' => $node->filter('.price')->count() ? $node->filter('.price')->text() : '0',
        'image' => $node->filter('.product-image img')->attr('src'),
        'rating' => $node->filter('.rating')->count() ? $node->filter('.rating')->text() : '0',
    ];
});
```

---

## 5. Save Data to a Database

### 5.1. Set Up the Database

Create a database called `walmart` and use the following SQL to define the schema:

```sql
CREATE TABLE goods (
    goods_id INT AUTO_INCREMENT PRIMARY KEY,
    subcategory_id INT,
    title VARCHAR(255),
    price VARCHAR(50),
    image TEXT,
    rating FLOAT
);
```

### 5.2. Insert Data Using Doctrine DBAL

Use Doctrine to insert product data:

```php
use Doctrine\DBAL\DriverManager;

$conn = DriverManager::getConnection([
    'dbname' => 'walmart',
    'user' => 'root',
    'password' => '',
    'host' => '127.0.0.1',
    'driver' => 'pdo_mysql',
]);

foreach ($products as $product) {
    $conn->insert('goods', $product);
}
```

---

## 6. Export to CSV

Finally, export the `goods` table to a CSV file:

```bash
SELECT * FROM goods INTO OUTFILE '/path/to/output.csv'
FIELDS TERMINATED BY ',' ENCLOSED BY '"'
LINES TERMINATED BY '\n';
```

You can now find all scraped data organized in a CSV file for further analysis.

---

## Conclusion

Scraping Walmart's catalog with PHP, Guzzle, Symfony DOM Crawler, and Doctrine DBAL provides a robust and efficient way to extract and store structured data. By following these steps, you can scrape product categories, subcategories, and items for any e-commerce project.

👉 **Stop wasting time on proxies and CAPTCHAs!** Simplify your scraping workflow with [ScraperAPI](https://bit.ly/Scraperapi) and start your free trial today!
