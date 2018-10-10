# Home-Assistant getting started using docker-compose

This is a basic example using docker compose to run:
* Home-Assistant (https://home-assistant.io)
* AppDaemon and HADashboard (https://appdaemon.readthedocs.io/en/latest/)
* Homebridge (https://github.com/nfarina/homebridge) more testing is needed
* MySql Database server (https://www.mysql.com)
* Mosquitto MQTT server (https://mosquitto.org)

## Prerequisite

To run the servers defined in this repository you need the following:

* Internet connection (downloading docker images)
* Docker and docker-composed installed and running on the computer (https://www.docker.com)
* Patience, a hunger to learn more, the ability to read documentation and search on google (or your favorite search engine)

## Running

1. Clone the repository or download and unzip the repository
2. Go to the root folder of the repository in a terminal (of some kind)
3. Run `docker-compose up`, this will download all the images and start them. To run in background use `docker-compose up -d` instead
    - If running in the background `docker-compose logs -f` can be used to display the output from the containers
4. When done run `docker-compose stop` to stop the container or `docker-compose down` to stop and remove the containers

## Configuration

For detailed configuration options read the respective applications documentation.

This repository defines a couple environment variables and default passwords though. Note passwords shall never be uploaded to places like github, but since this is an example using dummy password they are provided  here.

**Home-Assistant**

*./home-assistant/secrets.yaml*

Passwords set or used by home-assistant

Key | Description
--------------------------|---------------------------------------------------
http_password = `welcome` | The home-assistant password
mqtt_username = `homeassistant` | The MQTT username, defined in `./mosquitto/passwd`
mqtt_password = `secret` |  The MQTT password, defined in `./mosquitto/passwd`
database_connection_string = `mysql://homeassistant:secret@db/homeassistant` | The mysql connection string container to container, defined in `./mysql/secrets.env`

*./home-assistant/ha.env*

Environment variables made available in the home-assistant container

**AppDaemon**

*./appdaemon/secrets.yaml*

Key | Description
-------------------------------|------------------------------------------------
ad_api_key = `appdaemonapikey` | The api-key used for AppDaemon
ha_api_url = `http://home-assistant:8123` | The container to container home-assistant url
ha_api_key = `welcome` | The home-assistant password, defined in `./home-assistant/secrets.yaml`

**Mosquitto**

Username and passwords are defined in `./mosquitto/passwd` (currently homeassistant:secret)

**MySql**

The `./mysql/secrets.env` defines username, database and passwords (both for root and home-assistant)

*Note: if any of the .env files are changed, the containers needs to be rebuilt. (`docker-compose rm -f` or `docker-compose down` and then `docker-compose up`)*

**Homebridge**

Coming shortly...

*./homebridge/config.json*

Se configuration options at https://github.com/nfarina/homebridge and https://github.com/home-assistant/homebridge-homeassistant

*./homebridge/homebridge.env*

The `./homebridge/homebridge.env` defines override values for used versions, and mode 

### MQTT password

The passwords for mqtt is stored in a hashed format in `./mosquitto/passwd`. To generate new password run the folling commands

```bash
host$ docker exec -it homeassistant-mqtt sh # To enter the container

container# /usr/bin/mosquitto_passwd -c /tmp/passwd username # Change the username
# We don't get the Password: prompt as expected, write the password twice anyway
container# cat /tmp/passwd # Print the created password file so the string can be copied 
username:$6$NWi+OE9FbDoYt5nZ$3oAbkQ8XN1sTA9BC6YiedMNVa65BlepncqZo6I9bUQCcN/JKEeMx4qNOji7iFji61mRnjHiwM7vIkV6EUnH/Aw==
container# exit
```

Use the copied string from the `cat` command (username:$6...Q==) above and paste it into `./mosquitto/passwd` to replace the dummy password (remember to update the secrets in ./home-assistant/secrets.yaml). 

## Image versions

Some images defined in the docker-compose.yaml file does not have a specified version, they use e.g. latest. To ensure that the images are up-to-date the command `docker-compose pull` can be executed. If there are any updated versions they will be downloaded and used the next time the containers are built.

## Ignore files when using git

If git is going to be used for version control, we don't want the real passwords to be stored. I have prepared the .gitignore with commented out ignore statements for secret and env files.

The easiest way to get the ignore correct is to:

1. Edit the .gitignore
  - Remove the hash before #secrets.yaml, #secrets.env, #ha.env
  - Add any other files that should be ignored
2. Remove my git folder (so you don't get my history) `rm -r ./.git/`
3. Create new git repository `git init`
4. Add all files `git add -A`
5. Check the status of the files/folders that will be added to git `git status` if more than wanted repeat from beginning
6. Commit the files `git commmit -m "Initial commit"
7. Push to remote, check with git hosting providers for remote urls and push commands... 

## Why this?

I created this repository to give a starting point for anyone wanting the get home-assistant up and running quickly. No guarantees are given....



