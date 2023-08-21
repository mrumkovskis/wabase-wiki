# DummyBank install guide

>Note: This guide is of Linux only!

### List of required software to run the project:
* [JDK](https://adoptium.net/) 
* [SBT](https://www.scala-sbt.org/)
* [Docker](https://www.docker.com/) 


### Step 1: Get the code
First, pull the project from [https://github.com/nikitajegorovs](https://github.com/nikitajegorovs)

### Step 2: Get the database
Wabase is tuned to work with [Postgresql](https://www.postgresql.org/). The easiest way to install the database is 
by running the 
```bash
docker compose up -d
```
command from `$PATH_TO_PROJECT/db/` folder. 

### Step 3: Get the server running locally
The final step is to start the server.
Run the commands below from the project's root.
In order to do this, firstly, 
we need to get the config 
file

```bash
cp ./src/main/resources/application.conf.sample ./src/main/resources/application.conf
```
Finally, we can start the server by:

```bash
sbt
```

In the oppened dialog start the server by runnnig:]

```bash
reStart
```



