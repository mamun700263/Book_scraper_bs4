
In this tutorial, we'll learn how to scrape book data from a website using BeautifulSoup, a powerful Python library for web scraping. 

### 🚀 **What’s the Plan?**  
Before diving in, let's break down the three main questions:  
- **Which website?**  
- **What data are we scraping?**  
- **What tools will we use?**  

---

## 📚 **Scraping Book Data from Books to Scrape**  
We'll be scraping book information from the website: [Books to Scrape](https://books.toscrape.com/).  

### ✅ **What We'll Scrape:**  
- **Title**  
- **Rating**  
- **Price**  
- **Image URL**  
- **Availability** (Whether it's in stock or not)  

### 🔧 **Technologies Used:**  
- **BeautifulSoup** (For parsing HTML and extracting data)  
- **Requests** (For making HTTP requests to fetch the website's content)  
- **Jupyter Notebook** (Great for testing and running code in small chunks)  

Using Jupyter Notebook is super helpful because it lets you test code snippets step-by-step, making debugging and experimentation much easier.

---




### 🔍 **Step 1: Setting Up the Environment**  
First, we need to create a **virtual environment** to keep our packages isolated. This helps prevent conflicts between packages for different projects.  

```bash
python3 -m venv venv            # Create a virtual environment
source venv/bin/activate        # Activate on Linux / Mac
venv\Scripts\activate           # Activate on Windows
```

Once activated, your terminal should show `(venv)` at the beginning, indicating that the environment is active.  

---


### 📦 **Step 2: Installing Necessary Packages**  
Let's install the necessary packages.  
- `bs4`: Provides **BeautifulSoup** for parsing HTML data.  
- `requests`: Allows us to make HTTP requests to get the web page's content.  
- `jupyter`: We'll use Jupyter Notebook to break down our code into small chunks and visualize the results quickly.  

```bash
pip install bs4 jupyter requests
```

---

### 📒 **Step 3: Creating a Jupyter Notebook File**  
Now that the packages are installed, let’s create a Jupyter Notebook file. Using Jupyter Notebook makes it easier to test, debug, and visualize data step-by-step.   

```bash
touch main.ipynb  # For Linux/Mac
```


---



### 📂 **Step 4: Importing Necessary Libraries**  
Now, let's import the packages we just installed:  
```python
import requests     # For making HTTP requests to fetch the website's HTML content.
from bs4 import BeautifulSoup  # For parsing and extracting data from the HTML content.
```
We are now ready to start scraping! 🚀  

---



### 🌐 **Step 5: Accessing the Website**  
It's good practice to save the URL of the target website. Now, we'll send a request to the website and check the response. If the status code is `200`, it means the request was successful, and we can proceed.  

```python
website = "https://books.toscrape.com/"
response = requests.get(website)

# Checking if the website is accessible
print(response.status_code)  # Should print 200 if successful
```
📌 **Note:** Status codes are server responses to our request. `200` means success, while other codes (e.g., `404`, `500`) indicate errors.

---




### 🍜 **Step 6: Making a Soup**  
Before we can eat the website, we have to cook it. And BeautifulSoup is our chef! What BeautifulSoup does is make the website’s HTML more readable and accessible by converting it into a soup (structured format) we can easily work with.  

We tell BeautifulSoup **what to cook (`response.text`)** — which is the raw HTML content we received from our request — and **how to cook it (`html.parser`)** — which helps BeautifulSoup parse the HTML correctly.

```python
soup = BeautifulSoup(response.text, 'html.parser')
title = soup.find('title').text.strip()
print(title)  # 'All products | Books to Scrape - Sandbox'
```
📌 **Note:** `html.parser` is the built-in parser in Python that allows BeautifulSoup to understand and navigate the HTML structure.

---


### 📦 **Step 7: Finding All Books**  
Now, we're diving into the less code, more reading part. This step requires inspecting the HTML to identify the tags, classes, and IDs we're interested in.  

Our main goal is to scrape book data. To do this, **right-click on the website and select 'Inspect'**. This will open the developer tools, where we can see the HTML structure.  

By examining the website, we can see that books are displayed inside `<li>` tags within a `<ul>` tag. More importantly, each book is represented by an `<article>` tag with the class `product_pod`.  
 
Since our soup is ready, we'll use our **spoon** — the `find_all()` method — to scoop out all the books. This method returns a list of all matching elements.

```python
books = soup.find_all('article', class_="product_pod")
print(len(books))  # Should print 20 (default books per page)
```

📌 **Explanation:**  
- `find_all()` is used here because we want to gather **all articles** on the page, not just one.  
- We're checking the length of the list (`len(books)`) to confirm we got all 20 books from the page.


---



### 🔍 **Step 8: Extracting Data from a Single Book**  
Instead of eating the whole soup at once and risking a mess, we'll start with a small spoonful — extracting data from a single book to ensure our code works before applying it to everything.

```python
book = books[0]  # Working with the first book
```

---



### 📖 **Extracting Book Title**  
The book title is usually larger text compared to other elements, so we can start by looking for an `<h>` tag. 

Let's inspect the book, and indeed, we find an `<h3>` tag with an `<a>` tag inside it that contains the title. We can extract the title like this:

```python
book_title = book.find('h3').a.text
print(book_title)
# 'A Light in the ...'
```

However, we can do even better! Upon a closer look, we notice the full title is inside the `title` attribute of the `<a>` tag. We can access this attribute directly:

```python
book_title = book.find('h3').a['title']  # Getting the full title
print(book_title)
```

---




### ⭐ **Extracting Book Rating**  
This part is pretty interesting! You might be wondering, "How do we know the number of stars for each book?" 

At first glance, you won't find anything that directly indicates the number of stars. Are we going to count the stars? Nah, that would be way too messy. And they're all looking the same. So what do we do?

Well, look closely at the class name. It says something like `star-rating Two`, and there are exactly two stars filled with color. This is a huge clue! We see that this pattern works across all books, so it's our escape door — we need to grab that second class name.

The ratings are stored as class names within a `<p>` tag. But be careful! There are other `<p>` tags, too. If we use `find_all()`, we'll get a list of all of them, and that’s not what we want. We can use `find()` instead to get the first `<p>` tag. Then we’ll access the class attribute and pick the last class name, which tells us the rating.

To make this easier, let's create a dictionary to map text-based ratings to numbers:

```python
rating_stars = {
    'One': 1,
    'Two': 2,
    'Three': 3,
    'Four': 4,
    'Five': 5
}

rating = book.find('p')['class'][-1]  # Get the last class name which indicates the rating
stars = rating_stars.get(rating, 0)
print(stars)
```

---





### 💲 **Extracting Price**  

Money, money, money. Who doesn't love it? 😅

Now, let’s move on to extracting the price of the book. In our soup bowl, if we look closely, we see a partition dedicated to the price — it has the class name `product_price`. So, we’ll dip our `find()` spoon into that partition and scoop out the first `<p>` tag.

Looks good, but wait! There's a little surprise in our scoop. The text we get looks like this: `Â£51.77`. That doesn’t make any sense! 🤔 

But, luckily, our trusty chef, Python, suggests using indexing to skip the weird character. By doing this, we get the actual price.

Here’s the updated code:

```python
price = book.find('div', class_="product_price").find('p')
print(price)  # Â£51.77
price = book.find('div', class_="product_price").find('p').text[1:]
print(price)  # £51.77
```

---


This explanation is fun and easy to follow! However, I’d suggest making the analogy a bit more clear while maintaining that playful tone. Here’s an adjusted version:

---

### 📸 **Extracting Image URL**  

Our junior chef has a crush on a girl, but he doesn’t have a photo of her. The image is restricted from being saved or downloaded. So, as a good friend, I helped him get the image URL. Want to know how? Well, let’s use this book scraping task as an example!

If you look closely, there’s only one image in all the books. This makes it super easy to grab the `<img>` tag using `find()`. Inside that tag, you’ll find the `src` attribute, which contains the image source.

But there’s a catch. Our chef isn’t as clever as you, so I decided to help him out. I added the base URL of the website to the `src` attribute to get the actual image URL. 

Here’s how you can do it too:

```python
image_url = f'{website}{book.find("img")["src"]}'
print(image_url)
```

---


### 📦 **Checking Availability**  

Now that we know how to grab the image URL, let's check if our book is actually in stock. We don’t want to stock up on books that are out of stock, right? 😄

To find out if the book is available, we’ll look at the second `<p>` tag inside the `<div class="product_price">`. It tells us whether the book is in stock or not. We’ll use `find_all()` to grab all the `<p>` tags, and then use `[1]` to get the second one. To avoid extra spacing, we can use the `strip()` method to clean it up.

Here’s how we do it:

```python
in_stock = book.find('div', class_="product_price").find_all('p')[1].text.strip()
print(in_stock)
```

---


### 🔄 **Step 9: Putting It All Together in a Function**  

To keep things clean, let’s wrap everything into a function:
We will use the try and except . so that our programe should not crash in the middle
```python
def extractor(book):
    try:
        book_title = book.find('h3').a['title']
    except:
        book_title = "Not found"

    try:
        rating = book.find('p')['class'][-1]
        stars = rating_stars.get(rating, 0)
    except:
        stars = 0

    try:
        price = book.find('div', class_="product_price").find('p').text[1:]
    except:
        price = "00.00"

    try:
        image_url = f'{website}{book.find("img")["src"]}'
    except:
        image_url = ""

    try:
        in_stock = book.find('div', class_="product_price").find_all('p')[1].text.strip()
    except:
        in_stock = "Unknown"

    return {
        "Title": book_title,
        "Stars": stars,
        "Price": price,
        "Availability": in_stock,
        "Image": image_url
    }
```

---

### 🔍 **Step 10: Extracting Data from All Books**  
now as you know we have all books in out books list so we will extract each book using for loop and the fuction we made
```python
book_items = []

for book in books:
    item = extractor(book)
    book_items.append(item)

# Check the extracted data
for item in book_items[:5]:
    print(item)
```



### 🎉 **And that’s it!**  
You now have a fully functional scraper for **Books to Scrape**. 

---

