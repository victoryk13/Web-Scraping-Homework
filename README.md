

```python
from bs4 import BeautifulSoup as bs
import pandas as pd
import requests as req
from splinter import Browser
import pymongo
```


```python
url = 'https://mars.nasa.gov/news/'
response = req.get(url)
soup = bs(response.text, 'html.parser')
```


```python
title_section = soup.find('div', class_='content_title')
news_title = title_section.find('a').text.strip()
news_p = soup.find('div', class_='rollover_description_inner').text.strip()
```


```python
executable_path = {'executable_path': 'chromedriver.exe'}
browser = Browser('chrome', **executable_path, headless=False)
url = 'https://www.jpl.nasa.gov/spaceimages/?search=&category=Mars'
browser.visit(url)
browser.click_link_by_partial_text('FULL IMAGE')

html = browser.html
soup = bs(html, 'html.parser')

featured_image_medium = soup.find('a', class_='button fancybox')
featured_image_link = featured_image_medium['data-link']
featured_image_link_url = 'https://www.jpl.nasa.gov' + featured_image_link
featured_image_link_url

browser.visit(featured_image_link_url)
html = browser.html
soup = bs(html, 'html.parser')
```


```python
featured_image_large = soup.find('figure', class_='lede')
featured_image_endpoint = featured_image_large.find('img')['src']
featured_image_url = 'https://www.jpl.nasa.gov' + featured_image_endpoint
```


```python
url = 'https://twitter.com/marswxreport?lang=en'
response = req.get(url)
soup = bs(response.text, 'html.parser')
mars_weather = soup.find('p', class_='TweetTextSize TweetTextSize--normal js-tweet-text tweet-text').text
```


```python
url = 'http://space-facts.com/mars/'
tables = pd.read_html(url)
df = tables[0]
df.columns = ['description', 'value']
df.set_index('description', inplace=True)
html_table = df.to_html()
html_table = html_table.replace('\n', '')
```


```python
executable_path = {'executable_path': 'chromedriver.exe'}
browser = Browser('chrome', **executable_path, headless=False)
url = 'https://astrogeology.usgs.gov/search/results?q=hemisphere+enhanced&k1=target&v1=Mars'
browser.visit(url)

html = browser.html
soup = bs(html, 'html.parser')

title_list = []
url_list = []
item_list = soup.find_all('div', class_ = 'item')

for x in range (0, len(item_list)):
    title = item_list[x].find('h3').text
    title = title.rsplit(' ', 1)[0]
    title_list.append(title)
    
    link_endpoint = item_list[x].find('a', class_ = 'itemLink product-item')['href']
    hemisphere_link = 'https://astrogeology.usgs.gov' + link_endpoint
    
    browser.visit(hemisphere_link)
    html = browser.html
    soup = bs(html, 'html.parser')
    link_div = soup.find('div', class_ = 'downloads')
    full_image_link = link_div.find('a')['href']
    url_list.append(full_image_link)
```


```python
hemisphere_image_urls = []

for x in range (0, len(title_list)):
    hemisphere_dict = {"title": title_list[x], "img_url": url_list[x]}
    hemisphere_image_urls.append(hemisphere_dict)
```
