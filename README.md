# bitcoin-analyzer project

By [Clément Ronzon](https://www.linkedin.com/in/clemrz/). Licensed under [MIT License](https://choosealicense.com/licenses/mit/).

This is the implementation of a Bitcoin price analyzer.
This price analyzer displays a graph with the price trends of the Bitcoin.

It is set to display the last 24 hours by, a datepicker range filter allows the user to change the timeline range.

When the filter is changed, the graph is redrawn to reflect the new filter.

The price analyzer fetches historical Bitcoin prices from a public URL (Yahoo). It stores it into a database (cron job). A REST API serves the data to the front-end.

This repository wraps two submodules, [front-end](https://github.com/ClemRz/bitcoin-analyzer-front) and [back-end](https://github.com/ClemRz/bitcoin-analyzer-back).
  
## Deployment with docker-compose

### Requirements

Docker version `19.03.0+`

### Steps

Clone this repository on your machine:

```shell script
$ git clone --recurse-submodules https://github.com/ClemRz/bitcoin-analyzer.git
$ cd bitcoin-analyzer
```

Make sure you rename `bitcoin-analyzer-back/src/.env.example` to `bitcoin-analyzer-back/src/.env` and fill in the database access information.

When using docker-compose it is important to set the host and the port accordingly:

```
DB_HOST=ba_back_mysql8
DB_PORT=3306
```

The rest of the back-end `.env` variables can be set as you wish, both the PHP application and Docker will take them into account.

Make sure the `bitcoin-analyzer-front/.env` is set like so:

```shell script
REACT_APP_API_URL=http://localhost:8081/api/%1/%2/%3.json
```

#### For development

At the root of the project, run:

```shell script
$ docker-compose -f bitcoin-analyzer-back/docker-compose.yml -f docker-compose.yml up
```

Then visit http://localhost:3000

#### For production

At the root of the project, run:

```shell script
$ docker-compose -f bitcoin-analyzer-back/docker-compose.yml -f docker-compose.prod.yml up
```

Then visit http://localhost

## Individual modules deployment

If you want to deploy the front and the back individually, please follow these instructions:

 - Deploying the front-end: https://github.com/ClemRz/bitcoin-analyzer-front/blob/master/README.md
 - Deploying the back-end: https://github.com/ClemRz/bitcoin-analyzer-back/blob/master/README.md