# DummyBank install guide

> Note: This guide is for Linux only!

## Setting up the environment

Before we start off with wabase, let's set up the test environment, so that you can try the features yourself as you go.
We are going to use Swagger to send different HTTP requests to a local wabase server and see what the response is.


### List of required software to run the project:

* [JDK](https://adoptium.net/)
* [SBT](https://www.scala-sbt.org/)
* [Docker](https://www.docker.com/)

### Step 1: Get the code

First, pull the project from [wabase-bank](https://github.com/muntis/wabase-bank)

```bash
git pull
cd DummyBank
```

### Step 2: Get the database

Wabase is tuned to work with [PostgresSQL](https://www.postgresql.org/) specifically. The easiest way to install the
database is by running the compose script. This script will create a docker container with PostgreSQL database already
configured and filled with necessary data.

```bash
docker compose up -d
```

### Step 3: Get the server running locally

The final step is to start the server. Run the commands below from the project's root. In order to do this, firstly, we
need to get the config file

```bash
cp ./src/main/resources/application.conf.sample ./src/main/resources/application.conf
cp ./.env.example ./.env
```

#TODO fix .env files

Finally, we can start the server by:

```bash
sbt
```

In the oppened dialog, start the server by runnnig

```bash
reStart
```

Now you have the server running. You can open

