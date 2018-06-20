# Python Tricks

## logging -- configuration and use

Create `logconf.json` under the root folder of project

```javascript
{ "version": 1,
 "disable_existing_loggers": false,
 "formatters": { "wy_prefer": { "format__old_toDelete": "\n[%(asctime)s] [%(levelname)s] %(name)-12s \n%(message)s", "format": "\n[%(asctime)s] [%(levelname)s] %(name)s :: %(funcName)s (Ln.%(lineno)d) \n%(message)s\n", "datefmt": "%Y-%m-%d %H:%M:%S" } },
 "handlers": { "console": { "class": "logging.StreamHandler", "level": "DEBUG", "formatter": "wy_prefer", "stream": "ext://sys.stdout" },
 "INFO_file_handler": { "class": "logging.handlers.RotatingFileHandler", "level": "INFO", "formatter": "wy_prefer", "filename": "log/INFO.log", "maxBytes": 10485760, "backupCount": 20, "encoding": "utf8" },
 "ERROR_file_handler": { "class": "logging.handlers.RotatingFileHandler", "level": "ERROR", "formatter": "wy_prefer", "filename": "log/ERROR.log", "maxBytes": 10485760, "backupCount": 20, "encoding": "utf8" } },
 "loggers": { "": { "level": "DEBUG", "handlers": ["console", "INFO_file_handler", "ERROR_file_handler"] } }}
```



