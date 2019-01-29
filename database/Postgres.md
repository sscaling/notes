Logging
-------

Query logs to file ([Official docs](https://www.postgresql.org/docs/9.1/runtime-config-logging.html)\)

### postgresql.conf

```
log_destination = 'csvlog'
log_statement = 'all'
logging_collector = t
```
