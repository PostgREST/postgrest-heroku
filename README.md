## DBAPI on Heroku

The best way to build an API, now for Heroku.

```sh
heroku create --stack=cedar --buildpack https://github.com/begriffs/dbapi-heroku.git
heroku addons:add heroku-postgresql
heroku config:set ANONYMOUS_ROLE=foo

git push heroku master
```

### Data migration

This buildpack includes [sqitch](http://sqitch.org).

```sh
heroku run sqitch deploy
```
