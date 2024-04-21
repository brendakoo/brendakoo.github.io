## Webscrap with Beautiful Soup

**Project description:** Using "beautifulsoup" to extract OMRON product prices from online retailers for comparison purpose.
Information required are:
- Description 
- Price

### 1. Import Libraries

```python
import pandas as pd

from urllib.request import urlopen
from bs4 import BeautifulSoup
```
### 2. Create list to store product name, model and prices
```python
product_desc=[] 
product_model=[] 
prices=[] 
```
### 3. Get the last page of searched  items

```python
url = "https://sg.rs-online.com/web/c/relays/general-purpose-relays/non-latching-relays/?applied-dimensions=4294965904&rpp=100&pn=1"
html = urlopen(url)
soup = BeautifulSoup(html, 'lxml')
ul = soup.find('ul', class_='pagination pull-left')

pages = [li.text for li in ul.find_all('li')]
last_page = pages[-2] 
print(last_page)
last_page = int(last_page)
```
### 4. Extract items in all pages with for loop
First, I have a "for loop" to go through each page, and use BeautifulSoup to turn it into a python object.
Next, I had to inspect the webpage to find the div_container and attribute to pick the required information.
Hence, the next "for loop" is to extract every name, model and prices in the page.  

```python
for i in range(1,last_page+1):
#specify url
    url = "https://sg.rs-online.com/web/c/relays/general-purpose-relays/non-latching-relays/?applied-dimensions=4294965904&rpp=100&pn={}".format(i)
    html = urlopen(url)

    #turn it into python object with beautiful soup
    #soup allows you to extract info about website
    soup = BeautifulSoup(html, 'lxml') #'lxml' is the html parser
    div_container = soup.find('tbody', class_='content') 
    
    # loop
    for a in div_container.findAll('tr', attrs={'class':'resultRow'}):
        name=a.find('a', href = True, attrs={'class':'product-name'})
        model=a.find('span', attrs={'class':'small-link'})
        price=a.find('span', attrs={'class':'col-xs-12 price text-left'})
        product_desc.append(name.text)
        product_model.append(model.text)
        prices.append(price.text)  
```

### 5. Store it in a dataframe and write to a csv file
Lastly, I create a dataframe to store the lists of required information and write it in a csv file.
```python
# create dataframe
df = pd.DataFrame({'Product Desc':product_desc,'Price':prices,'Model':product_model})
df.head()

#write to csv
df.to_csv('prices_webscrap.csv', encoding='utf-8', index = False)

```
