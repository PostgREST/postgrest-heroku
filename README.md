## Postgrest on Heroku

The best way to build an API, now for Heroku.

Make a git repo with anything in it. The repo is just a pretext to
push to Heroku.

Note that **the Heroku PostgreSQL addon will not work** because it
does not support having multiple database roles. I would recommend
spinning up a Postgres database with [Amazon
RDS](https://aws.amazon.com/rds/). Make sure you create it in
the **Virginia region** because that's where Heroku's dynos are
and it will decrease latency between the server and db.

Next install the [Heroku Toolbelt](https://toolbelt.heroku.com/)
and run these commands.

```bash
# first set up a new app
heroku create --stack=cedar --buildpack https://github.com/begriffs/postgrest-heroku.git

# now fill in the values specific to your database
heroku config:set AUTH_ROLE=foo
heroku config:set AUTH_PASS=abc
heroku config:set ANONYMOUS_ROLE=bar
heroku config:set DB_NAME=my_db
heroku config:set DB_HOST=foo.rds.amazonaws.com
heroku config:set DB_PORT=5432
heroku config:set DB_POOL=10

heroku config:set POSTGREST_VER=0.2.4.9

git push heroku master
```

To determine the best value for `DB_POOL` ask the database by
running this SQL:

```SQL
show max_connections;
```
