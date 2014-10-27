## DBAPI on Heroku

The best way to build an API, now for Heroku.

```sh
heroku create --stack=cedar --buildpack https://github.com/begriffs/dbapi-heroku.git

heroku config:set DATABASE_URL=postgres://[amazon-rds-instance]
heroku config:set ANONYMOUS_ROLE=foo
heroku config:set DB_POOL=10

git push heroku master
```

Be sure to include a `?connect_timeout=5` parameter to the postgres connection
uri to prevent the app from hanging when being awoken from dyno sleep.
