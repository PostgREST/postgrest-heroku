## PostgREST on Heroku

The best way to build an API, now for Heroku.

Updated for PostgREST v7.0.1.

NOTE: **The free tier Heroku PostgreSQL addon will not work** because it does
not support having multiple database roles. **Heroku Postgres** paid tiers do
support multiple database roles, though you'll have to create them through
[Heroku Postgres
Credentials](https://devcenter.heroku.com/articles/heroku-postgresql-credentials).

We recommend spinning up a Postgres database with
[ElephantSQL](https://www.elephantsql.com/) for free tier usage.

### To Deploy PostgREST on Heroku

1.  Log into Heroku using the [Heroku
    CLI](https://devcenter.heroku.com/articles/heroku-cli):

    ```bash
    # If you have multiple Heroku accounts, use flag '--interactive' to switch between them
    heroku login --interactive
    ```

2.  Create a new Heroku app using this Heroku buildpack:

    ```bash
    heroku apps:create --buildpack https://github.com/formdeploy/postgrest-heroku.git
    ```

3.  Set Heroku environment variables:

    ```bash
    heroku config:set POSTGREST_VER=7.0.1
    heroku config:set DB_URI=postgres://postgrest_test:postgrest111@postgrest-test.crbxuv1p3j1c.us-west-1.rds.amazonaws.com/postgrest_test
    heroku config:set DB_SCHEMA=public
    heroku config:set DB_ANON_ROLE=postgrest_test
    heroku config:set DB_POOL=60
    ```

4.  Create the Heroku dyno:

    ```bash
    git push heroku master
    ```

### Configuring PostgreSQL

-   To determine the best value for PostgREST environment variable `DB_POOL`,
    ask the database by running this SQL:

    ```sql
    show max_connections;
    ```
