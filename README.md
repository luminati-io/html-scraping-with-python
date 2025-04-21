# HTML Web Scraping with Python

[![Bright Data Promo](https://github.com/luminati-io/LinkedIn-Scraper/raw/main/Proxies%20and%20scrapers%20GitHub%20bonus%20banner.png)](https://brightdata.com/)

This tutorial covers HTML fundamentals and how to collect, parse, and process web data using Python:

- [Introduction to HTML Structure](#introduction-to-html-structure)
- [Setting Up Your Python Environment](#setting-up-your-python-environment)
- [Extracting Complete HTML from a Webpage](#extracting-complete-html-from-a-webpage)
- [Targeting Specific HTML Elements](#targeting-specific-html-elements)
- [Interacting with Page Elements](#interacting-with-page-elements)
- [Saving Extracted Data to CSV](#saving-extracted-data-to-csv)
- [Final Thoughts](#final-thoughts)

Want a comprehensive Python web scraping resource? [Check out our detailed guide](https://brightdata.com/blog/how-tos/web-scraping-with-python).

## Introduction to HTML Structure

Before diving into scraping, it's important to understand the building blocks of HTML.

HTML consists of tags that define a webpage's structure and components. For instance, `<h1> Text </h1>` defines heading text, while `<a href=""> link </a>` creates a hyperlink.

HTML attributes provide additional element information. For example, the `href` attribute in an `<a> </a>` tag specifies the link destination URL.

[Classes and IDs](https://www.scaler.com/topics/html/class-and-id-in-html/) are crucial attributes for precisely identifying page elements. Classes group similar elements for consistent styling with CSS or manipulation via JavaScript. Classes are referenced using `.class-name`.

On W3Schools, class groupings appear like this:

```html
<div class="city">
  <h2>London</h2>
  <p>London is the capital of England.</p>
</div>

<div class="city">
  <h2>Paris</h2>
  <p>Paris is the capital of France.</p>
</div>

<div class="city">
  <h2>Tokyo</h2>
  <p>Tokyo is the capital of Japan.</p>
</div>

```

Notice how each title and city section is enclosed in a `div` sharing the same `city` class.

In contrast, IDs must be unique to individual elements (no two elements can share an ID). For example, these H1 elements have unique IDs for individual styling/manipulation:

```html
<h1 id="header1">Hello World!</h1>

<h1 id="header2">Lorem Ipsum Dolor</h1>

```

The syntax for targeting ID elements is `#id-name`.

## Setting Up Your Python Environment

This guide uses Python because of its many HTML scraping libraries and beginner-friendly syntax. To verify if Python is installed, run this command in PowerShell (Windows) or Terminal (macOS):

```sh
python3
```

If Python is installed, you'll see the version number; otherwise, you'll get an error message. If needed, [download and install Python](https://www.python.org/downloads/).

Next, create a folder named `WebScraper` and inside it create a file called `scraper.py`. Open this file in your preferred integrated development environment (IDE). We'll use [Visual Studio Code](https://code.visualstudio.com/) in this guide:

![VSCode showing the project](https://github.com/luminati-io/html-scraping-with-python/blob/main/images/an-image-showing-where-VSC-is-used-1.png)

An IDE is a comprehensive tool that enables developers to write code, debug, test programs, create automations, and more. You'll use it to develop your HTML scraper.

Now, isolate your global Python installation from your scraping project by creating a virtual environment. This prevents dependency conflicts and keeps your project organized.

Install the `virtualenv` library with this command:

```sh
pip3 install virtualenv
```

Navigate to your project folder:

```sh
cd WebScraper
```

Create a virtual environment:

```sh
python<version> -m venv <virtual-environment-name>
```

This creates a directory for all packages and scripts within your project folder:

![Virtual environment folder creation](https://github.com/luminati-io/html-scraping-with-python/blob/main/images/Command-to-create-a-folder-for-all-the-packages-and-scripts-1.png)

Now activate your virtual environment using the appropriate command for your system:

```sh
source <virtual-environment-name>/bin/activate #In MacOS and Linux

<virtual-environment-name>/Scripts/activate.bat #In CMD

<virtual-environment-name>/Scripts/Activate.ps1 #In Powershell

```

When activated successfully, your virtual environment name will appear on the left side of your terminal:

![Virtual environment activation indicator](https://github.com/luminati-io/html-scraping-with-python/blob/main/images/The-name-of-your-virtual-environment-1.png)

With your virtual environment active, install a web scraping library. Options include [Playwright](https://playwright.dev/), [Selenium](https://www.selenium.dev/), [Beautiful Soup](https://www.crummy.com/software/BeautifulSoup/), and [Scrapy](https://scrapy.org/). For this tutorial, we'll use [Playwright](https://playwright.dev/python/docs/intro) because it's user-friendly, supports multiple browsers, handles dynamic content, and offers headless mode (scraping without a GUI).

Run `pip install pytest-playwright` to install Playwright, then install the required browsers with `playwright install`.

Now you're ready to start web scraping.

## Extracting Complete HTML from a Webpage

The first step in any scraping project is selecting your target website. For this tutorial, we'll use [this e-commerce test site](https://webscraper.io/test-sites/e-commerce/static).

Next, identify what information you want to extract. Initially, we'll capture the entire HTML content of the page.

After identifying your scraping target, start coding your scraper. In Python, begin by [importing the necessary Playwright libraries](https://playwright.dev/python/docs/library). Playwright offers two API types: [sync and async](https://stackoverflow.com/questions/65439214/what-are-the-differences-between-python-playwright-sync-vs-async-apis). Since we're not writing asynchronous code, import the sync library:

```python
from playwright.sync_api import sync_playwright
```

After importing the sync library, define a Python function:

```python
def main():
    #Rest of the code will be inside this function
```

All your web scraping code will reside within this function.

Typically, to access a website's information, you open a browser, create a tab, and visit the site. For scraping, translate these actions into code using Playwright. According to their [documentation](https://playwright.dev/python/docs/library), you can call the imported `sync_api` and launch a browser:

```python
with sync_playwright() as p:
        browser = p.chromium.launch(headless=False)
```

Setting `headless=False` allows you to see the browser content during execution.

After launching the browser, open a new tab and navigate to your target URL:

```python
page = browser.new_page()
try:
  page.goto("https://webscraper.io/test-sites/e-commerce/static")
except:
  print("Error")
```

> **Note:**
> 
> Add these lines below the previous browser launch code. All this code belongs inside the main function in a single file.

This code wraps the `goto()` function in a [try-except block](https://www.w3schools.com/python/python_try_except.asp) for better error handling.

Just as you wait for a site to load when entering a URL, add a waiting period to your code:

```python
page.wait_for_timeout(7000) #millisecond value in brackets
```

> **Note:**
> 
> Add these lines below the previous code.

Finally, extract the complete HTML content from the page:

```python
print(page.content())
```

The complete HTML extraction code looks like this:

```python
from playwright.sync_api import sync_playwright

def main():
  with sync_playwright() as p:
        browser = p.chromium.launch(headless=False)   
        page = browser.new_page()
        try:
            page.goto("https://webscraper.io/test-sites/e-commerce/static")
        except:
            print("Error")

        page.wait_for_timeout(7000)
        print(page.content())

main()
```

In Visual Studio Code, the extracted HTML appears like this:

![Extracted HTML in VSCode](https://github.com/luminati-io/html-scraping-with-python/blob/main/images/The-extracted-HTML-in-VSC-1.png)

## Targeting Specific HTML Elements

While extracting an entire webpage is possible, web scraping becomes truly valuable when you focus on specific information. In this section, we'll extract only the laptop titles from the website's first page:

![Laptop titles to extract](https://github.com/luminati-io/html-scraping-with-python/blob/main/images/Showing-the-titles-we-are-going-to-extract-from-the-target-website-1.png)

To extract specific elements, understand the website's structure first. Right-click and select **Inspect** on the page:

![Using inspect on the target website](https://github.com/luminati-io/html-scraping-with-python/blob/main/images/Click-on-inspect-on-the-target-website-1.png)

Alternatively, use these keyboard shortcuts:
- macOS: **Cmd + Option + I**
- Windows: **Control + Shift + C**

Here's the structure of our target page:

![HTML structure of target website](https://github.com/luminati-io/html-scraping-with-python/blob/main/images/The-HTML-structure-of-the-target-website-1.png)

You can examine specific page elements using the selection tool in the top-left corner of the **Inspect** window:

![Inspecting specific elements](https://github.com/luminati-io/html-scraping-with-python/blob/main/images/How-to-inspect-specific-items-in-the-source-code-1.png)

Select one of the laptop titles in the **Inspect** window:

![Inspecting a laptop title](https://github.com/luminati-io/html-scraping-with-python/blob/main/images/Inspecting-one-of-the-titles-we-want-to-scrape-1.png)

You can see that each title is contained in an `<a> </a>` tag, wrapped by an `h4` tag, with the link having a `title` class. So we need to look for `<a href>` tags inside `<h4>` tags with a `title` class.

To create a targeted scraping program, import the required libraries, create a Python function, launch the browser, and navigate to the laptop page:

```python
from playwright.sync_api import sync_playwright

def main():
    with sync_playwright() as p:
        browser = p.chromium.launch(headless=False)
        page = browser.new_page()
        try:
            page.goto("https://webscraper.io/test-sites/e-commerce/static/computers/laptops")

        except:
            print("Error")

        page.wait_for_timeout(7000)
```

Note that we've updated the URL in the `page.goto()` function to point directly to the laptop listing page.

Now locate the target elements based on your structure analysis. Playwright provides [locators](https://playwright.dev/python/docs/locators) to find elements using various attributes:

- **`get_by_label()`** finds elements by their associated label
- **`get_by_text()`** finds elements containing specific text
- **`get_by_alt_text()`** finds images by their alt text
- **`get_by_test_id()`** finds elements by their test ID

See [the official documentation](https://playwright.dev/python/docs/locators) for more element location methods.

To extract all laptop titles, locate the `<h4>` tags that contain them. Use the `get_by_role()` locator to find elements by their function (buttons, checkboxes, headings, etc.):

```python
titles = page.get_by_role("heading").all()
```

Print the results to your console:

```python
print(titles)
```

The output shows an array of elements:

![Array of heading elements](https://github.com/luminati-io/html-scraping-with-python/blob/main/images/Array-of-elements-after-printing-the-titles-1.png)

This output doesn't show the titles directly, but references elements matching our criteria. We need to loop through these elements to find `<a>` tags with a `title` class and extract their text.

Use the [CSS locator](https://playwright.dev/python/docs/locators#locate-by-css-or-xpath) to find elements by path and class, and the `all_inner_texts()` function to extract their text:

```python
for title in titles:
  laptop = title.locator("a.title").all_inner_texts()
```

Running this code produces output like:

![Output of title extraction](https://github.com/luminati-io/html-scraping-with-python/blob/main/images/An-image-of-how-the-output-should-look-like-1.png)

To filter out empty arrays, add:

```python
if len(laptop) == 1:
  print(laptop[0])
```

Here's the complete code for this specific element scraper:

```python
from playwright.sync_api import sync_playwright

def main():
    with sync_playwright() as p:
        browser = p.chromium.launch(headless=False)
        page = browser.new_page()
        try:
            page.goto("https://webscraper.io/test-sites/e-commerce/static/computers/laptops")

        except:
            print("Error")

        page.wait_for_timeout(7000)

        titles = page.get_by_role("heading").all()

        for title in titles:
            laptop = title.locator("a.title").all_inner_texts()
            if len(laptop) == 1:
                print(laptop[0])

main()
```

## Interacting with Page Elements

Let's enhance our scraper to extract titles from multiple pages. We'll scrape titles from the first laptop page, navigate to the second page, and extract those titles as well.

Since we already know how to extract titles, we just need to learn how to navigate to the next page.

The website has pagination buttons at the bottom. We need to locate and click on the "2" button programmatically. Inspecting the page reveals that this element is a list item (`<li>` tag) with the text "2":

![Pagination element with text "2"](https://github.com/luminati-io/html-scraping-with-python/blob/main/images/The-required-element-has-an-inner-text-of-2-1.png)

We can use the `get_by_role()` selector to find a list item and the `get_by_text()` selector to find text containing "2":

```python
page.get_by_role("listitem").get_by_text("2", exact=True)
```

This finds an element matching both conditions: it must be a list item and have exactly "2" as its text.

The `exact=True` parameter ensures we match the exact text.

To click this button, modify the code:

```python
page.get_by_role("listitem").get_by_text("2", exact=True).click()
```

The `click()` function performs a click on the matched element.

Now wait for the page to load and extract titles again:

```python
page.wait_for_timeout(5000)

titles = page.get_by_role("heading").all()

for title in titles:
    laptop = title.locator("a.title").all_inner_texts()
    if len(laptop) == 1:
        print(laptop[0])
```

Your complete multi-page scraper code should look like this:

```python
from playwright.sync_api import sync_playwright

def main():
    with sync_playwright() as p:
        browser = p.chromium.launch(headless=False)
        page = browser.new_page()
        try:
            page.goto("https://webscraper.io/test-sites/e-commerce/static/computers/laptops")

        except:
            print("Error")

        page.wait_for_timeout(7000)

        titles = page.get_by_role("heading").all()

        for title in titles:
            laptop = title.locator("a.title").all_inner_texts()
            if len(laptop) == 1:
                print(laptop[0])
        
        page.get_by_role("listitem").get_by_text("2", exact=True).click()

        page.wait_for_timeout(5000)

        titles = page.get_by_role("heading").all()

        for title in titles:
            laptop = title.locator("a.title").all_inner_texts()
            if len(laptop) == 1:
                print(laptop[0])

main()
```

## Saving Extracted Data to CSV

Scraped data needs to be stored and analyzed to be useful. Now we'll create an advanced program that asks users how many laptop pages to scrape, extracts the titles, and saves them to a CSV file.

First, import the CSV library:

```python
import csv
```

Next, determine how to visit multiple pages based on user input.

Looking at the website's URL structure, we notice that each laptop page uses a URL parameter. For example, the second page URL is https://webscraper.io/test-sites/e-commerce/static/computers/laptops?page=2.

We can navigate to different pages by changing the `?page=2` parameter. Ask the user how many pages to scrape:

```python
pages = int(input("enter the number of pages to scrape: "))
```

To visit each page from 1 to the user-specified number, use a `for` loop:

```python
for i in range(1, pages+1):
```

The `range` function starts at the first value (1) and ends before the second value (pages+1). For example, `range(1,5)` loops from 1 to 4.

Now visit each page by using the loop variable `i` as the URL parameter. We can insert variables into strings using [Python f-strings](https://www.geeksforgeeks.org/formatted-string-literals-f-strings-python/).

An f-string uses an `f` prefix before quotation marks, allowing variable insertion using curly brackets:

```python
print(f"The value of the variable is {variable_name_goes_here}")
```

For our scraper, use f-strings in the navigation code:

```python
try:
    page.goto(f"https://webscraper.io/test-sites/e-commerce/static/computers/laptops?page={i}")
except:
    print("Error")
```

Wait for the page to load and extract titles:

```python
page.wait_for_timeout(7000)
titles = page.get_by_role("heading").all()
```

Next, open a CSV file, loop through each title, extract the text, and write it to the file.

Open a CSV file with:

```python
with open("laptops.csv", "a") as csvfile:
```

We're opening `laptops.csv` in append mode (`a`). Using append means new data is added without erasing existing data. If the file doesn't exist, it will be created. [CSV supports several file modes](https://www.learnbyexample.org/reading-and-writing-csv-files-in-python/):

- **r**: Default mode, opens file for reading only
- **w**: Opens file for writing, overwrites existing data
- **a**: Opens file for appending, preserves existing data
- **r+**: Opens file for both reading and writing
- **x**: Creates a new file

Next, create a [writer object](https://docs.python.org/3/library/csv.html#csv.writer) to manipulate the CSV file:

```python
writer = csv.writer(csvfile)
```

Loop through each title element and extract the text:

```python
for title in titles:
    laptop = title.locator("a.title").all_inner_texts()
```

To filter out empty arrays and write valid titles to the CSV:

```python
if len(laptop) == 1:
    writer.writerow([laptop[0]])
```

The `writerow` function adds new rows to the CSV file.

Here's the complete CSV export scraper code:

```python
from playwright.sync_api import sync_playwright
import csv

def main():
    with sync_playwright() as p:
        browser = p.chromium.launch(headless=False)
        page = browser.new_page()
        pages = int(input("enter the number of pages to scrape: "))

        for i in range(1, pages+1):
            try:
                page.goto(f"https://webscraper.io/test-sites/e-commerce/static/computers/laptops?page={i}")

            except:
                print("Error")

            page.wait_for_timeout(7000)

            titles = page.get_by_role("heading").all()

            with open("laptops.csv", "a") as csvfile:
                writer = csv.writer(csvfile)
                for title in titles:
                    laptop = title.locator("a.title").all_inner_texts()
                    if len(laptop) == 1:
                        writer.writerow([laptop[0]])

        browser.close()             


main()
```

After running this code, your CSV file should look like:

![CSV file output example](https://github.com/luminati-io/html-scraping-with-python/blob/main/images/Example-of-how-the-CSV-file-should-look-like-1.png)

## Final Thoughts

While this guide demonstrates basic web scraping, [real-world scenarios](https://brightdata.com/use-cases) often present challenges such as CAPTCHAs, rate limits, site layout changes, and regulatory requirements. Bright Data offers [solutions](https://brightdata.com/products) for these challenges, including [advanced residential proxies](https://brightdata.com/proxy-types/residential-proxies) to improve scraping performance, a Web Scraper IDE for building scalable scrapers, and a [Web Unblocker](https://brightdata.com/products/web-unlocker) to access blocked sites.

Start your free trial today!
