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
    heroku apps:create ${YOUR_APP_NAME} --buildpack https://github.com/formdeploy/postgrest-heroku.git
    heroku git:remote -a ${YOUR_APP_NAME}
    ```

3.  Create an account on ElephantSQL, and [create an API
    key](https://customer.elephantsql.com/apikeys) to use the [ElephantSQL
    customer API](https://docs.elephantsql.com/). Run the following `curl`
    command in order to create a PostgreSQL instance on ElephantSQL:

    ```bash
    curl \
        -u :${YOUR_API_KEY} \
        -d "name=${YOUR_DB_NAME}&plan=turtle&region=amazon-web-services::us-east-1" \
        https://customer.elephantsql.com/api/instances
    ```

    Get the PostgreSQL DB URI from the ElephantSQL console and set as env
    variable `DB_URI`. Make sure the URI field is expanded before copying.

    Take the database name (with assumed backing role of identical name) and set
    as env variable `DB_ANON_ROLE`.

3.  Set Heroku environment variables:

    ```bash
    heroku config:set POSTGREST_VER=7.0.1
    heroku config:set DB_URI=${DB_URI}
    heroku config:set DB_SCHEMA=public
    heroku config:set DB_ANON_ROLE=${DB_ANON_ROLE}
    heroku config:set DB_POOL=60
    ```

4.  Create the Heroku dyno:

    ```bash
    git push heroku master
    ```

Your Heroku app should be live at `${YOUR_APP_NAME}.herokuapp.com`.

### Configuring PostgreSQL

-   To determine the best value for PostgREST environment variable `DB_POOL`,
    ask the database by running this SQL:

    ```sql
    show max_connections;
    ```
