# Infrastructure Setup

## Jenkins

```
docker run -dt --name jenkins --network=host --restart always -e JENKINS_OPTS="--httpPort=8000" -v /opt/jenkins_data:/var/jenkins_home jenkinsci/jenkins
```

## Docker Registry

```
docker run -d -p 5000:5000 -v /opt/registry_data:/var/lib/registry --restart always --name registry registry:2
```

## Pact Broker

Refer: `https://github.com/DiUS/pact_broker-docker/blob/master/POSTGRESQL.md`

- 1. create postgres

```
docker run --name pactbroker-db -e POSTGRES_PASSWORD=ThePostgresPassword -e POSTGRES_USER=admin -e PGDATA=/data/pgdata -v /opt/postgresql:/data -d postgres
```

- 2. create tables

```
docker run -it --link pactbroker-db:postgres --rm postgres sh -c 'exec psql -h "$POSTGRES_PORT_5432_TCP_ADDR" -p "$POSTGRES_PORT_5432_TCP_PORT" -U admin'
> CREATE USER pactbrokeruser WITH PASSWORD 'TheUserPassword';
> CREATE DATABASE pactbroker WITH OWNER pactbrokeruser;
> GRANT ALL PRIVILEGES ON DATABASE pactbroker TO pactbrokeruser;
```

- 3. create pact broker

```
docker run --name pactbroker --link pactbroker-db:postgres -e PACT_BROKER_DATABASE_USERNAME=pactbrokeruser -e PACT_BROKER_DATABASE_PASSWORD=TheUserPassword -e PACT_BROKER_DATABASE_HOST=postgres -e PACT_BROKER_DATABASE_NAME=pactbroker -d -p 2000:80 dius/pact-broker
```

## Mongo

- 1. create mongo

```
docker run -d --name mongo -v /opt/mongo:/data/db --network=host mongo --auth
```

- 2. setup user

```
docker exec -it mongo mongo admin
> db.createUser({ user: 'root', pwd: 'root', roles: [ { role: "root", db: "admin" } ] });
```

- 3. test connection

```
docker run -it --rm mongo mongo -u root -p root --authenticationDatabase admin 10.202.128.102/demo-db
> db.getName();
```

## MySQL

```
docker run -d --name mysql -v /opt/mysql:/var/lib/mysql --network=host -e MYSQL_ROOT_PASSWORD=root mysql
```

