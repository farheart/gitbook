# Python Tricks

## Logging -- Configuration and Usage

* Create `logconf.json` under the root folder of project

```javascript
{
    "version": 1,

    "disable_existing_loggers": false,

    "formatters": {
        "wy_prefer": {
            "format__old_toDelete": "\n[%(asctime)s] [%(levelname)s] %(name)-12s  \n%(message)s",
            "format": "\n[%(asctime)s] [%(levelname)s] %(name)s :: %(funcName)s (Ln.%(lineno)d)  \n%(message)s\n",
            "datefmt": "%Y-%m-%d %H:%M:%S"
        }
    },

    "handlers": {
        "console": {
            "class": "logging.StreamHandler",
            "level": "DEBUG",
            "formatter": "wy_prefer",
            "stream": "ext://sys.stdout"
        },

        "INFO_file_handler": {
            "class": "logging.handlers.RotatingFileHandler",
            "level": "INFO",
            "formatter": "wy_prefer",
            "filename": "log/INFO.log",
            "maxBytes": 10485760,
            "backupCount": 20,
            "encoding": "utf8"
        },

        "ERROR_file_handler": {
            "class": "logging.handlers.RotatingFileHandler",
            "level": "ERROR",
            "formatter": "wy_prefer",
            "filename": "log/ERROR.log",
            "maxBytes": 10485760,
            "backupCount": 20,
            "encoding": "utf8"
        }
    },

    "loggers": {
        "": {
            "level": "DEBUG",
            "handlers": ["console", "INFO_file_handler", "ERROR_file_handler"]
        }
    }
}
```

* Then do the configuration by `Python` code

```python
import os
import logging
import logging.config

def config_logging(
    default_path='logconf.json',
    default_level=logging.INFO,
    env_key='LOG_PATH'
):
    """Setup logging configuration
    """
    value = os.getenv(env_key, None)
    path = value if value else default_path
    if os.path.exists(path):
        with open(path, 'rt') as f:
            logging.config.dictConfig(json.load(f))
    else:
        logging.basicConfig(level=default_level)
```

* Below is how to use the `logging`.

```python
import logging
logger = logging.getLogger(__name__)

def main():
    config_logging()
    logger.debug(">> I am a debug")
    logger.info(">> I am an info")
```

The outputs are as below

```plain
[2018-06-20 09:39:29] [DEBUG] __main__ :: main (Ln.3)
>> I am a debug


[2018-06-20 09:39:29] [INFO] __main__ :: main (Ln.4)
>> I am an info
```

[DONE]


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