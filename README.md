---
description: >-
  This document is used to record all Python Pandas tricks that I found useful
  during my development
---

# Pandas Tricks

## Pandas -- Avoid convert Int to Float when do df.T.to\_dict\(\).values\(\)

Instead of use list\(df.T.to\_dict\(\).values\(\)\), use **\[{c:getattr\(r, c\) for c in df} for r in df.itertuples\(\)\]**

> Ref: [https://stackoverflow.com/questions/37897527/get-python-pandas-to-dict-with-orient-records-but-without-float-cast](https://stackoverflow.com/questions/37897527/get-python-pandas-to-dict-with-orient-records-but-without-float-cast)
>
> ```text
> import pandas as pd
>
> df = pd.DataFrame({'a':[3,2], 'b':[42.0,3.14]})
> result = [{col:getattr(row, col) for col in df} for row in df.itertuples()]
> print(result)
> # [{'a': 3, 'b': 42.0}, {'a': 2, 'b': 3.1400000000000001}]
> ```



## Have you had a chance to answer the previous question?

Yes, after a few months we finally found the answer. Sadly, Mike is on vacations right now so I'm afraid we are not able to provide the answer at this point.



