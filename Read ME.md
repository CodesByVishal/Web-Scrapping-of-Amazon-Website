# Amazon Laptop Scraper

This project provides a Python script to scrape laptop listings from Amazon India. The scraper extracts details like the laptop title, image, rating, price, and ad/organic status across multiple pages. The data is then saved to a CSV file for further analysis.

## Features

- **Scraping Multiple Pages**: Automatically determines the total number of pages for the given search query (`laptops`) and scrapes data from all available pages.
- **Data Extraction**: Extracts key details like:
  - Laptop title
  - Laptop image URL
  - Rating
  - Price
  - Ad/Organic listing status
- **Data Cleaning**: Filters out irrelevant or incomplete data (e.g., missing prices) and saves a clean version of the dataset.
- **Random Delays**: Introduces random delays between requests to avoid detection as a bot.
- **CSV Export**: Saves the scraped data to a CSV file with a timestamped filename.

## Installation

1. Clone the repository to your local machine:

```bash
git clone https://github.com/CodesByVishal/amazon-laptop-scraper.git
```

2. Install the required Python libraries:

```bash
pip install requests beautifulsoup4 pandas
```

3. **Google Colab Setup**: The script is designed to be run in a Google Colab environment to save the output directly to Google Drive.

## Usage

### 1. Import Libraries

```python
import requests
from bs4 import BeautifulSoup
import pandas as pd
from datetime import datetime
import time
import random
import re
```

### 2. Get Max Pages

This function determines the number of pages available for the search query:

```python
def get_max_pages(url, headers):
    ...
```

### 3. Scrape Laptop Data

This function scrapes data for each laptop on a given page:

```python
def scrape_amazon_laptops(page, headers):
    ...
```

### 4. Scraping the Data

Use the following code to scrape data across multiple pages and save it as a CSV:

```python
url = "https://www.amazon.in/s?k=laptops"
headers = {
    "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/110.0.0.0 Safari/537.36",
    "Accept-Language": "en-US,en;q=0.9"
}

max_pages = get_max_pages(url, headers)
print(f"Found {max_pages} pages.")  # Example: Found 20 pages.

all_laptops = []

for page in range(1, max_pages + 1):
    print(f"Scraping page {page}...")
    all_laptops.extend(scrape_amazon_laptops(page, headers))
    time.sleep(random.uniform(2, 5))  # Random delay to avoid detection
```

### 5. Save the Data

Once data is collected, it is converted into a DataFrame and saved as a CSV file:

```python
df = pd.DataFrame(all_laptops, columns=['Image', 'Title', 'Rating', 'Price', 'Ad/Organic Result'])

# Filtering out products without price
df = df[df['Price'] != 'No Price']
df.reset_index(drop=True, inplace=True)

# Save to Google Drive (if using Google Colab)
from google.colab import drive
drive.mount('/content/drive')
csv_filename = f'laptops_data_{datetime.now().strftime("%Y%m%d_%H%M%S")}.csv'
csv_path = f"/content/drive/MyDrive/YourFolder/{csv_filename}"
df.to_csv(csv_path, index=False)

print(f"Data saved to Google Drive at: {csv_path}")
```

### 6. Clean the Data

To clean the data, you can remove rows with missing or irrelevant information (e.g., missing titles, ratings, or images):

```python
cleaned_df = df.copy()
cleaned_df.reset_index(drop=True, inplace=True)
df_irrelevent_items = df[(df['Rating'] == 'No Rating') & (df['Title'] == 'No Title') & (df["Image"] == 'No Image')]
cleaned_df.drop(df_irrelevent_items.index, inplace=True, errors="ignore")

# Save the cleaned DataFrame to a CSV file
csv_filename = f'Cleaned_laptops_data_{datetime.now().strftime("%Y%m%d_%H%M%S")}.csv'
cleaned_df.to_csv(csv_filename, index=False)

print(f"Data saved to {csv_filename}")
```

### Output

The output will be a CSV file with the following columns:

- `Image`: The URL of the laptop's image
- `Title`: The name of the laptop
- `Rating`: The user rating of the laptop (if available)
- `Price`: The price of the laptop
- `Ad/Organic Result`: Whether the listing is an advertisement or an organic result

## Example Output (first 5 rows)

| Image | Title | Rating | Price | Ad/Organic Result |
| --- | --- | --- | --- | --- |
| ![Image URL](https://m.media-amazon.com/images/I/71EJdFiOw5...) | ASUS Zenbook A14 | No Rating | 1,29,990 | Sponsored |
| ![Image URL](https://m.media-amazon.com/images/I/81v5jg9AAK...) | ASUS Vivobook 16 | 5.0 | 1,29,990 | Sponsored |
| ![Image URL](https://m.media-amazon.com/images/I/71VRrc7V-P...) | ASUS Vivobook Go 14 | 3.9 | 1,29,990 | Organic |
| ![Image URL](https://m.media-amazon.com/images/I/61fDHkQ6Mq...) | Acer Aspire Lite | 3.8 | 1,29,990 | Organic |
| ![Image URL](https://m.media-amazon.com/images/I/41XYpjPaft...) | Lenovo V14 G3 | 3.0 | 1,29,990 | Organic |

## Contributing

Feel free to fork this repository, make improvements, or submit pull requests. Ensure that your code adheres to the existing structure and includes necessary tests.

