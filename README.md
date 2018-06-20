---
description: Notes about useful Pandas (Python) tricks.
---

# Pandas Tricks

## Avoid convert Int to Float when do df.T.to\_dict\(\).values\(\)

Instead of use list\(df.T.to\_dict\(\).values\(\)\), use **\[{c:getattr\(r, c\) for c in df} for r in df.itertuples\(\)\]**

> Ref: [https://stackoverflow.com/questions/37897527/get-python-pandas-to-dict-with-orient-records-but-without-float-cast](https://stackoverflow.com/questions/37897527/get-python-pandas-to-dict-with-orient-records-but-without-float-cast)
>
> ```python
> import pandas as pd
>
> df = pd.DataFrame({'a':[3,2], 'b':[42.0,3.14]})
> result = [{col:getattr(row, col) for col in df} for row in df.itertuples()]
> print(result)
> # [{'a': 3, 'b': 42.0}, {'a': 2, 'b': 3.1400000000000001}]
> ```



## Set display format

`pd.set_option('display.width',200)  
pd.set_option('display.max_rows',20)  
pd.set_option('display.max_columns', 15)`

## Iterate DataFrame

```python
df = readCSV(inCSV)
for i,r in df.iterrows():
    if pd.isnull(r['OSRM_meter']):
        url = createURL(r)
```

## Read from CSV

```python
def function(fn):
    return pd.read_csv(fn, index_col=false)
```

## Access URL and retrieve JSON

```python
# Python3.6
import urllib.request
import json
def retrieveJSON(url):
    result = None
    # with urllib.request.urlopen("http://maps.googleapis.com/maps/api/geocode/json?address=google") as url:
    with urllib.request.urlopen(url) as req:
        result = json.loads(req.read().decode())
    return result
```

