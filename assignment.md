# Assignment

## Brief

Write the Python codes for the following questions.

## Instructions

Paste the answer as Python in the answer code section below each question.

### Question 1

Question: The scraping of `https://www.scrapethissite.com/pages/forms/` in the last section assumes a hardcoded (fixed) no of pages. Can you improve the code by removing the hardcoded no of pages and instead use the `»` button to determine if there are more pages to scrape? Hint: Use a `while` loop.

```python
def parse_and_extract_rows(soup: BeautifulSoup):
    """
    Extract table rows from the parsed HTML.

    Args:
        soup: The parsed HTML.

    Returns:
        An iterator of dictionaries with the data from the current page.
    """
    header = soup.find('tr')
    headers = [th.text.strip() for th in header.find_all('th')]
    teams = soup.find_all('tr', 'team')
    for team in teams:
        row_dict = {}
        for header, col in zip(headers, team.find_all('td')):
            row_dict[header] = col.text.strip()
        yield row_dict
```

Answer:

```python

# Check the pagination bar on the first page
r = requests.get("https://www.scrapethissite.com/pages/forms/?page_num=1")
soup = BeautifulSoup(r.text, "html.parser")

# Print entire pagination HTML
pagination = soup.find("ul", class_="pagination")
print("Pagination HTML:")
print(pagination)

# Print the specific Next button to verify existence
next_btn = soup.find("a", {"aria-label": "Next"})
print("\nNext button detected:")
print(next_btn)

#Start Web Scraping
import requests
from bs4 import BeautifulSoup
import time

rows = []
page = 1

while True:
    print(f"Scraping in progress for page {page}...")

    try:
        r = requests.get(
            f"https://www.scrapethissite.com/pages/forms/?page_num={page}",
            timeout=3
        )
    except requests.exceptions.Timeout:
        print(f"Page {page} timed out. Retrying...")
        time.sleep(0.5)
        continue

    soup = BeautifulSoup(r.text, "html.parser")

    # Extract rows from the current page
    for row_dict in parse_and_extract_rows(soup):
        rows.append(row_dict)

    # Detect the Next page button
    next_btn = soup.find("a", {"aria-label": "Next"})

    if next_btn:
        page += 1
       
    else:
        print("Reach end of all pages. Congrats, Scraping complete!")
        break

#Display them in Pandas in a scrollable table, i change the color fo the table. 

from IPython.display import HTML
import pandas as pd

df = pd.DataFrame(rows)

HTML(f'''
<div style="
    height:400px;
    overflow:auto;
    border:1px solid #ccc;
    padding:10px;
    border-radius:6px;
    background-color:white;
    color:black;
">
    {df.to_html(index=False)}
</div>

```

## Submission

- Submit the URL of the GitHub Repository that contains your work to NTU black board.
- Should you reference the work of your classmate(s) or online resources, give them credit by adding either the name of your classmate or URL.
