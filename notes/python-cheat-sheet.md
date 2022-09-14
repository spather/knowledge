# My Python Cheat Sheet

## Read a CSV file

```python
import csv 

with open("path/to/file.csv", newline="") as csvfile:
    reader = csv.reader(csvfile)
    next(reader) # skip header (omit if no header)
    for row in reader:
      # Here `row` is a list of strings, one for each value in the line
```

## Download an image from a URL

```python
import requests

url = ... # some URL pointing to an image
img_data = requests.get(url).content
with open("path/to/target.jpeg", "wb") as file:
    file.write(img_data)
```

## Get the Parts of a URL
```python
from urllib.parse import parse_qs, urlparse

url = ... # some URL
parts = urlparse(url) # after this, `parts` is a dict with keys for each of the parts of the url
query_string_parts = parse_qs(parts.query) # after this, `query_string_parts` is a dict with one item per query string entry
```

## Make a directory
```python
from pathlib import Path

target_dir = Path("path/to/target")
target_dir.mkdir(exist_ok=True, parents=True)
```

## Format date/time as a string
Useful for generating unique filenames

```python
from datetime import datetime

datetime.now().strftime("%Y%m%d-%H%M%S")
```
