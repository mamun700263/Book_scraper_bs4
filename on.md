

## 📚 **Scraping Book Data from Books to Scrape**  
We'll be scraping book information from the website: [Books to Scrape](https://books.toscrape.com/).  

### ✅ **What we'll scrape:**  
- **Title**  
- **Rating**  
- **Price**  
- **Image URL**  
- **Availability** (In stock or not)  

### 🔧 **Technologies Used:**  
- **BeautifulSoup**  
- **Requests**  
- **Jupyter Notebook**

---

### 🔍 **Step 1: Setting Up the Environment**  
First, we need to create a virtual environment to keep our packages isolated.

```bash
python3 -m venv venv
source venv/bin/activate  # For Linux / Mac
venv\Scripts\activate      # For Windows
```

---

### 📦 **Step 2: Installing Necessary Packages**  
```bash
pip install bs4 jupyter requests
```

---

### 📒 **Step 3: Creating a Jupyter Notebook File**  
```bash
touch main.ipynb
```
Launch Jupyter Notebook and select the correct kernel.

---

### 📂 **Step 4: Importing Necessary Libraries**  
```python
import requests
from bs4 import BeautifulSoup
```

---

### 🌐 **Step 5: Accessing the Website**  
```python
website = "https://books.toscrape.com/"
response = requests.get(website)

# Checking if the website is accessible
print(response.status_code)  # Should print 200 if successful
```

---

### 🍜 **Step 6: Making a Soup**  
```python
soup = BeautifulSoup(response.text, 'html.parser')
title = soup.find('title').text.strip()
print(title)  # 'All products | Books to Scrape - Sandbox'
```

---

### 📦 **Step 7: Finding All Books**  
Books are stored inside `<article>` tags with the class `product_pod`.

```python
books = soup.find_all('article', class_="product_pod")
print(len(books))  # Should print 20 (default books per page)
```

---

### 🔍 **Step 8: Extracting Data from a Single Book**  
We'll first extract data from a single book to make sure our code works, then use it for all books.

```python
book = books[0]  # Working with the first book
```

---

### 📖 **Extracting Book Title**  
```python
book_title = book.find('h3').a['title']  # Getting full title
print(book_title)
```

---

### ⭐ **Extracting Book Rating**  
Ratings are stored as class names within a `<p>` tag.  
We'll create a dictionary to convert text-based ratings to numbers.

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
```python
price = book.find('div', class_="product_price").find('p').text[1:]
print(price)
```

---

### 📸 **Extracting Image URL**  
```python
image_url = f'{website}{book.find("img")["src"]}'
print(image_url)
```

---

### 📦 **Checking Availability**  
```python
in_stock = book.find('div', class_="product_price").find_all('p')[1].text.strip()
print(in_stock)
```

---

### 🔄 **Step 9: Putting It All Together in a Function**  
To keep things clean, let’s wrap everything into a function:

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
```python
book_items = []

for book in books:
    item = extractor(book)
    book_items.append(item)

# Check the extracted data
for item in book_items[:5]:
    print(item)
```

---

### 💾 **Step 11: Saving Data to a CSV File**  
```python
import pandas as pd

df = pd.DataFrame(book_items)
df.to_csv('books_data.csv', index=False)
```

---

### 🎉 **And that’s it!**  
You now have a fully functional scraper for **Books to Scrape**. 

---

