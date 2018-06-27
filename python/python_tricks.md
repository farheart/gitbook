# Python Tricks

## Pause running by `time.sleep()`
```python
import time

time.sleep(0.005)  # sleep 5 milli-seconds
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