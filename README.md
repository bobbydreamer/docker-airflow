# docker-airflow

* This repository is copied from [puckel/docker-airflow](https://github.com/puckel/docker-airflow). 
* Copied on 06/Feb/2020
* [puckel/docker-airflow image](https://hub.docker.com/r/puckel/docker-airflow) can be found here
* Purpose is to test Udacity Data Engineering - Capstone project

Few(little, very little) customizations have been done as to easily deploy it in ec2 instance.
1. Start Ubuntu EC2 instance
1. Update & Upgrade ubuntu
1. Install tree, zip, docker, docker-compose, aws-cli
1. Pull docker image puckel/docker-airflow
1. git clone this repo
1. Docker build
1. Review docker-compose-LocalExecutor.yml
    - Check to see if examples are required : ```LOAD_EX=n ```
    - Volume mounting : ```- ./dags:/usr/local/airflow/dags```


## Run the below commands in ec2 instance
```
cd /home/ubuntu
printf 'ubuntu\nubuntu\n' | sudo passwd ubuntu
printf 'root\nroot\n' | sudo passwd root
sudo usermod -aG sudo ubuntu
HOME=/home/ubuntu
export HOME
AIRFLOW_HOME=/home/ubuntu/docker-airflow
export AIRFLOW_HOME

sudo apt-get update -yqq
sudo apt-get upgrade -yqq
sudo apt-get -y install tree
sudo apt-get -y install zip
sudo apt-get -y install docker.io
sudo apt-get -y install docker-compose

pip3 install "boto3>=1.9,<2"

sudo docker pull puckel/docker-airflow

git clone https://github.com/puckel/docker-airflow.git

cd docker-airflow/

# Building the container using the Dockerfile
sudo docker build --rm --build-arg AIRFLOW_DEPS="datadog,dask" -t puckel/docker-airflow .
sudo docker build --rm --build-arg PYTHON_DEPS="flask_oauthlib>=0.9" -t puckel/docker-airflow .
# (or)
sudo docker build --rm --build-arg AIRFLOW_DEPS="datadog,dask" --build-arg PYTHON_DEPS="flask_oauthlib>=0.9" -t puckel/docker-airflow .

sudo docker-compose -f docker-compose-LocalExecutor.yml up -d

```

## Use below commands for other operations

    sudo docker ps

    nano docker-compose-LocalExecutor.yml

    sudo docker exec -it <<container-id/name>> /bin/bash

    sudo docker-compose -f docker-compose-LocalExecutor.yml down


---

## Installation
Pull the image from the Docker repository.

    docker pull puckel/docker-airflow

## Build

Optionally install [Extra Airflow Packages](https://airflow.incubator.apache.org/installation.html#extra-package) and/or python dependencies at build time :

    docker build --rm --build-arg AIRFLOW_DEPS="datadog,dask" -t puckel/docker-airflow .
    docker build --rm --build-arg PYTHON_DEPS="flask_oauthlib>=0.9" -t puckel/docker-airflow .

or combined

    docker build --rm --build-arg AIRFLOW_DEPS="datadog,dask" --build-arg PYTHON_DEPS="flask_oauthlib>=0.9" -t puckel/docker-airflow .

Don't forget to update the airflow images in the docker-compose files to puckel/docker-airflow:latest.

## Usage

By default, docker-airflow runs Airflow with **SequentialExecutor** :

    docker run -d -p 8080:8080 puckel/docker-airflow webserver

If you want to run another executor, use the other docker-compose.yml files provided in this repository.

For **LocalExecutor** :

    docker-compose -f docker-compose-LocalExecutor.yml up -d

NB : If you want to have DAGs example loaded (default=False), you've to set the following environment variable :

`LOAD_EX=n`

    docker run -d -p 8080:8080 -e LOAD_EX=y puckel/docker-airflow

If you want to use Ad hoc query, make sure you've configured connections:
Go to Admin -> Connections and Edit "postgres_default" set this values (equivalent to values in airflow.cfg/docker-compose*.yml) :
- Host : postgres
- Schema : airflow
- Login : airflow
- Password : airflow

## UI Links

- Airflow: [IPv4 Public IP:8080](http://IPv4PublicIP:8080/)
    - Get the IPv4 Public IP Address from EC2 Console

## Running other airflow commands

If you want to run other airflow sub-commands, such as `list_dags` or `clear` you can do so like this:

    docker run --rm -ti puckel/docker-airflow airflow list_dags

or with your docker-compose set up like this:

    docker-compose -f docker-compose-CeleryExecutor.yml run --rm webserver airflow list_dags

You can also use this to run a bash shell or any other command in the same environment that airflow would be run in:

    docker run --rm -ti puckel/docker-airflow bash
    docker run --rm -ti puckel/docker-airflow ipython
