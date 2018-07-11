# Python Tricks

## Format number with leading `0`
```python
import time

time.sleep(0.005)  # sleep 5 milli-seconds
```


## Pause running by `time.sleep()`
```python
>>> print('data/osrmDist_{0:07d}_{1:07d}.csv'.format(0, 999))
data/osrmDist_0000000_0000999.csv
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


## Access URL by Proxy
```python
proxy_support = urllib.request.ProxyHandler({
    'http': 'http://aaaaaaaa.com:8080' 
})
opener = urllib.request.build_opener(proxy_support)
urllib.request.install_opener(opener)
url = "http://router.project-osrm.org/route/v1/driving/-94.19466,36.35858;-112.40870,34.55132?overview=false"
with urllib.request.urlopen(url) as req:
    print(req.read().decode())
```