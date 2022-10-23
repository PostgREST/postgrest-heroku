## PostgREST on Heroku

The best way to build an API, now for Heroku.

Updated for PostgREST >= v9.0.0

NOTE: **The free tier Heroku PostgreSQL addon will work with some limitations** because it does
not support having multiple database roles. **Heroku Postgres** paid tiers do
support multiple database roles, though you'll have to create them through
[Heroku Postgres
Credentials](https://devcenter.heroku.com/articles/heroku-postgresql-credentials) as explained below.

### To Deploy PostgREST on Heroku

1.  Log into Heroku using the [Heroku
    CLI](https://devcenter.heroku.com/articles/heroku-cli):

    ```bash
    # If you have multiple Heroku accounts, use flag '--interactive' to switch between them
    heroku login --interactive
    ```

2.  Create a new Heroku app using this Heroku buildpack:

    ```bash
    mkdir ${YOUR_APP_NAME}
    cd ${YOUR_APP_NAME}
    git init .

    heroku apps:create ${YOUR_APP_NAME} --buildpack https://github.com/abernicchia-heroku/postgrest-heroku.git
    heroku git:remote -a ${YOUR_APP_NAME}
    ```

3.  Create a new Heroku Postgres add-on attached to the app and keep notes of the assigned add-on name (e.g. postgresql-curly-58902) referred later as ${HEROKU_PG_DB_NAME}
    ```bash
    heroku addons:create heroku-postgresql:standard-0 -a ${YOUR_APP_NAME}
    ```

4.  Create the necessary user roles according to the 
    [PostgREST documentation](https://postgrest.org/en/stable/auth.html):

    ```bash
    heroku pg:credentials:create —name api_user -a ${YOUR_APP_NAME}
    heroku addons:attach ${HEROKU_PG_DB_NAME} --credential api_user -a ${YOUR_APP_NAME}
    ```

5.  Connect to the Postgres database and create some sample data:

    ```bash
    heroku psql -a ${YOUR_APP_NAME}
    ```

    ```
    create table api.todos (
    id serial primary key,
    done boolean not null default false,
    task text not null,
    due timestamptz
    );

    insert into api.todos (task) values
    ('finish tutorial 0'), ('pat self on back');

    grant usage on schema api to api_user;
    grant select on api.todos to api_user;
    ```

5.  Create the `Procfile`:

    ```bash
    web: PGRST_SERVER_HOST=0.0.0.0 PGRST_SERVER_PORT=${PORT} PGRST_DB_URI=${PGRST_DB_URI:-${DATABASE_URL}} ./postgrest-${POSTGREST_VER}
    ```

    Set the following environment variables on Heroku - POSTGREST_VER is mandatory to select and build the required PostgREST release:
    ```
    heroku config:set POSTGREST_VER=10.0.0
    heroku config:set DB_SCHEMA=api
    heroku config:set DB_ANON_ROLE=api_user
    ```

    See https://postgrest.org/en/stable/configuration.html#environment-variables for the full list of environment variables.

6.  Build and deploy your app:

    ```bash
    git add Procfile
    git commit -m "PostgREST on Heroku"
    git push heroku master
    ```

Your Heroku app should be live at `${YOUR_APP_NAME}.herokuapp.com`.

7.  Test your app
    From a terminal display the application logs:
    ```bash
    heroku logs -t
    ```
    From a different terminal retrieve with curl the records previously created:
    ```bash
    curl https://${YOUR_APP_NAME}.herokuapp.com/todos
    ```
    and test that any attempt to modify the table via a read-only user is not allowed:
    ```bash
    curl https://${YOUR_APP_NAME}.herokuapp.com/todos -X POST \
     -H "Content-Type: application/json" \
     -d '{"task": "do bad thing"}'
    ```    
