## Postgrest on Heroku

The best way to build an API, now for Heroku.

```sh
heroku create --stack=cedar --buildpack https://github.com/begriffs/postgrest-heroku.git

heroku config:set AUTH_ROLE=foo
heroku config:set AUTH_PASS=abc
heroku config:set ANONYMOUS_ROLE=bar
heroku config:set DB_NAME=my_db
heroku config:set DB_HOST=foo.rds.amazonaws.com
heroku config:set DB_PORT=5432
heroku config:set DB_POOL=10

git push heroku master
```

Be sure to include a `?connect_timeout=5` parameter to the postgres connection
uri to prevent the app from hanging when being awoken from dyno sleep.
