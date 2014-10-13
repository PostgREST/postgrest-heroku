## DBAPI on Heroku

The best way to build an API, now for Heroku.

```sh
heroku create --stack=cedar --buildpack https://github.com/begriffs/dbapi-heroku.git

heroku config:set DATABASE_URL=postgres://[amazon-rds-instance]
heroku config:set ANONYMOUS_ROLE=foo

git push heroku master
```
