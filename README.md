## PostgREST on Heroku

The best way to build an API, now for Heroku.

Make a git repo with anything in it. The repo is just a pretext to
push to Heroku.

Note that **the free tier Heroku PostgreSQL addon will not work** because it
does not support having multiple database roles. **Heroku Postgres** paid tiers do support multiple
database roles though you'll have to create them through [Heroku Postgres Credentials](https://devcenter.heroku.com/articles/heroku-postgresql-credentials).

We recommend spinning up a Postgres database with [Amazon
RDS](https://aws.amazon.com/rds/). Make sure you create it in
the **Virginia region** because that's where Heroku's dynos are
and it will decrease latency between the server and db.

Next install the [Heroku Toolbelt](https://toolbelt.heroku.com/)
and run these commands.

```bash
# first set up a new app
heroku apps:create --buildpack https://github.com/PostgREST/postgrest-heroku.git

# now fill in the values specific to your database
heroku config:set POSTGREST_VER=0.5.0.0
heroku config:set DB_URI=postgres://postgrest_test:postgrest111@postgrest-test.crbxuv1p3j1c.us-west-1.rds.amazonaws.com/postgrest_test
heroku config:set DB_SCHEMA=public
heroku config:set DB_ANON_ROLE=postgrest_test
heroku config:set DB_POOL=60

git push heroku master
```

To determine the best value for `DB_POOL` ask the database by
running this SQL:

```SQL
show max_connections;
```
