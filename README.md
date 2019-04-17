# openIMIS dockerized

| :bomb: Disclaimer : NOT FOR PRODUCTION USE :bomb: |
| --- |
| This repository provides a dockerized openIMIS (all components). It provides a quick setup for development, testing or demoing. ***It is NOT INTENDED FOR PRODUCTION USE.***. **The gateway is not secure** (no ssl,...) and must be adapted before being exposed to the internet! For demo, it is also configured with basic auth for external application accesses, while the intend is to have a certificate-based authentication.|
| The docker-compose currently only contains the openIMIS database, backend and gateway components. It will be completed as the other components are added to the platform (frontend,...) |


# First startup
First startup is special since it will create the necessary docker images and containers to run openIMIS.
To build necessary, docker images, docker-compose  relies on ***local*** docker files.
In order to build these images, you need to clone, next to `openimis-dist_dkr/` the following github repository:
* openimis-db_dkr
* openimis-be_py

From within `openimis-dist_dkr/` directory:
* create a `.env` file, providing the following variables:
```
 DB_HOST=<your database host, or db to use the demo docker 'db' service>
 DB_PORT=<your database port on the host, 1433 if you use the demo docker 'db' service>
 DB_NAME=<your database name, imis if you use the demo docker 'db' service>
 DB_USER=<your database user, sa if you use the demo docker 'db' service >
 DB_PASSWORD=<your database password, generate one if you use the demo docker 'db' service>
```
* If you use the demo docker 'db' service:
  * build and start the database docker image:  `docker-compose up db`
  (note: use --force-recreate if you already created the image but want to change the password)
  * restore the openIMIS default backup into the container:
    * `docker container ls` and spot the line (CONTAINER ID) with `openimis-dist_dkr_db` IMAGE name
    * `docker exec <CONTAINER ID> /restore.sh`
* build and start the gateway (and backend) docker image: `docker-compose up gateway`
  (note: at each start, openIMIS will apply the necessary database migrations to update the database scheme)
* register your openIMIS superuser in the gateway:
  * list running containers and spot the gateway: `docker container ls` (the gateway should be named `openimis-dist_dkr_gateway_1`)
  * connect to the gateway: `docker exec -it <CONTAINER ID> /bin/sh` (sh and not bash)
  * you should be in /script directory, if not, just `cd /script`)
  * in the gateway container, register your openIMIS superuser: `./add-user.sh <SUPERUSER_NAME> <SUPER_USER_PASSWORD>`
  Notes:
    * same procedure (add-user.sh) must be followed to add external applications accesses
    * in `/script`, there are also `remove-user.sh`and `update-user.sh`
* If connecting to brand new database, register your openIMIS superuser:
  * list running containers and spot the backend: `docker container ls` (the gateway should be named `openimis-dist_dkr_backend_1`)
  * connect to the gateway: `docker exec -it <CONTAINER ID> /bin/bash` (bash and not sh this time)
  * you should be in /openimis-be/openIMIS directory, if not, just `cd /openimis-be/openIMIS`)
  * register your openIMIS superuser: `python manage.py createsuperuser` (and follow instructions)

# stop /start
From within `openimis-dist_dkr/` directory:
To stop all docker containers: `docker-compose stop`
To (re-)start all docker containers: `docker-compose start` 