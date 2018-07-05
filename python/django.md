# Django Tricks

## To handle **“No 'Access-Control-Allow-Origin' header is present on the requested resource”** in django

> Ref: <https://stackoverflow.com/questions/22476273/no-access-control-allow-origin-header-is-present-on-the-requested-resource-i>

```
pip install django-cors-headers
```

Edit django settings.py
```
INSTALLED_APPS = (
    ...
    'corsheaders',
    ...
)

MIDDLEWARE_CLASSES = (
    ...
    'corsheaders.middleware.CorsMiddleware',
    'django.middleware.common.CommonMiddleware',
    ...
)

CORS_ORIGIN_ALLOW_ALL = True
```

Setting above to true allows all origins to be accepted.

References: <https://github.com/ottoyiu/django-cors-headers>