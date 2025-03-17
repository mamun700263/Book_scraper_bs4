
**plan**
- wher to scrape?
    - [books](https://books.toscrape.com/)
- what to scrape?
    - book title
    - rating
    - price
    - img url
    - in stock or not
- what technology to use?
    - beautiful soup 
    - request
    - jupyter
- where to store data? 
    - csv
    - excel
    - json 
- what technology to use data maintaing?
    - pandas

### Start

**Create virtual environment**
```bash
python3 -m venv venv
source venv/bin/activate 
```

**Install neccessary packeges**
```bash
pip install pandas bs4 jupyter requests
```
**Create a jupyter file**
```bash
touch main.ipynb
```
You might need to select the karnel

**Import neccessary files**
```py
import requests
from bs4 import BeautifulSoup
import pandas as pd 
```

**Get website**
```py
website = "https://books.toscrape.com/" 
response = requests.get(website)
response.status_code

#200
```

check the status code and get the website

**Make a soup**
```py
soup = BeautifulSoup(response.text,'html')
title = soup.find('title')
title.text.strip()

#'All products | Books to Scrape - Sandbox'
```

go to the website and inspect 
hover over a book and look which part of code is working for that particular book
and look for similarities with other books
most porvably you will find in most cases a li/ol/ul tag. 
in our case we found li 
inside that ther is a article tag which is common for all books  
all books have a class = "article pod"

to be more cocise we will use that too

**Get the books**
```py
books = soup.find_all('article', class_ = "product_pod")
len(books)
#20
```

from my personla experience i like to work with a single book at first so that 
i just use that part of code in the loop and the work gets easier

**work on the first book**
```py
book = books[0]
book_title = book.find('h3')
book_title.text

#'A Light in the ...'
```

but it is not the best what we can expect
just dive in it more and explore
if we notic that the code of that line looks like
**what the code is?**
```py
book_title
```

it looks like this
```html
<h3>
    <a href="catalogue/a-light-in-the-attic_1000/index.html" title="A Light in the Attic">
        A Light in the ...
    </a>
</h3>
```

look at the title attribute which has the full title of the book . lets try to get it

as it is inside the `a` tag so we need to access the `a` tag first then we can access the attribute easily

```py
book_title.a['title']
#A Light in the Attic
```

lets get the rating 
if you notic well you will see the rating class is in the first `p` tag . it means we can use find() to get it. if you want to make a numaric or interger rating you can see that . we can never understand from the start but if you notice in the class ther is acutally a number which indicates the rating. why not just get the class name and get the last word?
**Class name of ratings p tag**
```py
rating = book.find('p')
rating['class']
#['star-rating', 'Two']
```

we got a list of classes wher Two indicats the rating 

so lets make a dictionary and use it to convert the number to numaric form
```py
rating_stars = {
    'One':1,
    'Two':2,
    'Three':3,
    'Four':4,
    'Five':5,
}
stars = rating_stars[rating['class'][-1]]
stars

#2
```

lets get the pricing and avaiablity 
as we noticed that they are in a div with class name "product_price"
from my so lets get it

```py
price = book.find('div', class_ = "product_price").find('p').text[1:]
price
```
here what i did is i made the work smaller focusing on a small part . so i first grab the div with the class name and theree are more then 1 p tag in the div so i jus tused find() tag and got the price

lets get the image url
it is may be easiest part as there is only one image per article we can easly get it by find()

but wait they are incomplete url you will need to add the website link at the beggenign

```py
image_url =f'{website}{book.find('img')['src']}'
image_url
```

lets look i f my product is avail avble to not to do that we can just get to that particular div and get the tag and inside the text
