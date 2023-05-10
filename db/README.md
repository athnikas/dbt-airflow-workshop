# db set-up

By executing the following command we will deploy a Postgres database that will serve as our Data Warehouse for this workship.
Along with the database, we are also deploying PGAdmin so we can connect to our database and query our data.

```
docker compose up
```

To access PGAdmin, open `localhost:5050` in your browser. Credentials for both the db and PGAdmin can be found in the docker compose file.
To find the host for Postgres connection, open a new terminal and run the following command.

```
docker inspect postgres_db | grep IPAddress
```
