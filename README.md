## PostgREST on Heroku

The best way to build an API, now for Heroku.

Updated for PostgREST v7.0.1.

NOTE: **The free tier Heroku PostgreSQL addon will not work** because it does
not support having multiple database roles. **Heroku Postgres** paid tiers do
support multiple database roles, though you'll have to create them through
[Heroku Postgres
Credentials](https://devcenter.heroku.com/articles/heroku-postgresql-credentials).

We recommend spinning up a Postgres database with [Amazon
RDS](https://aws.amazon.com/rds/). Make sure you create it in
the **Virginia region** because that's where Heroku's dynos are
and it will decrease latency between the server and db.

Alternatively you could create it on [ElephantSQL](https://www.elephantsql.com/) for free tier usage.

### To Deploy PostgREST on Heroku

1.  Log into Heroku using the [Heroku
    CLI](https://devcenter.heroku.com/articles/heroku-cli):

    ```bash
    # If you have multiple Heroku accounts, use flag '--interactive' to switch between them
    heroku login --interactive
    ```

2.  Create a new Heroku app using this Heroku buildpack:

    ```bash
    heroku apps:create ${YOUR_APP_NAME} --buildpack https://github.com/PostgREST/postgrest-heroku
    heroku git:remote -a ${YOUR_APP_NAME}
    ```

3.  Create the necessary user roles according to the 
    [PostgREST documentation](https://postgrest.org/en/stable/auth.html) and 
    allow them role switch from the psql console:

    ```bash
    GRANT user123 TO authenticator;
    GRANT web_anon TO authenticator;
    ```

4.  Create a configuration file `postgrest.conf`:

    ```bash
    db-uri = "postgres://authenticator:mysecretpassword@postgresqlserver.com:5432/postgres"
    db-schema = "api"
    db-anon-role = "web_anon"
    db-pool = 10
    ```

    and the `Procfile`:

    ```bash
    ./env-to-config ./postgrest postgrest.conf
    ```

    It is also possible to set variables directly on Heroku:
    ```
    heroku config:set POSTGREST_VER=0.5.0.0
    heroku config:set DB_URI=postgres://postgrest_test:postgrest111@postgrest-test.crbxuv1p3j1c.us-west-1.rds.amazonaws.com/postgrest_test
    heroku config:set DB_SCHEMA=public
    heroku config:set DB_ANON_ROLE=postgrest_test
    heroku config:set DB_POOL=60
    ```

    To determine the best value for PostgREST environment variable `DB_POOL`,
    ask the database by running this SQL:

    ```sql
    show max_connections;
    ```

    See https://postgrest.org/en/stable/configuration.html for the full list of configuration parameters.

5.  Create the Heroku dyno:

    ```bash
    git push heroku master
    ```

Your Heroku app should be live at `${YOUR_APP_NAME}.herokuapp.com`.