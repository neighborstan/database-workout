## Testing speed increase of query execution by moving some data to Redis

### Problem

There is a MySQL relational database with a schema (country-city, language by country). And there is a frequent city query that slows down.

### Solution

Put all the data that are frequently requested into Redis (in memory storage of the key-value type).

## Run

1. Run MySQL server as a docker container  
```
docker run --name mysql -d -p 3306:3306 -e MYSQL_ROOT_PASSWORD=root --restart unless-stopped -v mysql:/var/lib/mysql mysql:8
```  


2. Deploy the [sql-дамп](src/main/resources/sql-dump/dump-hibernate-final.sql)


3.  Run the Redis server as a docker container
  - if you do the optional step «install [redis-insight](https://redis.com/redis-enterprise/redis-insight/), look at the data that is stored in Redis», then the command:  
  ```
  docker run -d --name redis-stack -p 6379:6379 -p 8001:8001 redis/redis-stack:latest
  ```
  - if without redis-insight, then enough:  
  ```
  docker run -d --name redis -p 6379:6379 redis:latest
  ```  


The difference is that the first one sends port 8001 to the local machine, to which you can connect to an external client to see what is stored inside.  


4.  Starts `Main` class

## Features

When testing, there is a feature - the data from MySQL is read only, so it can not be restarted between runs of our application. And in Redis they are written.  
Although trying to add a duplicate with the same key, the data will simply update, it is recommended to run the commands `docker stop redis-stack` and `docker rm redis-stack`.  
After that, bring up the redis container 
```
docker run -d --name redis-stack -p 6379:6379 -p 8001:8001 redis/redis-stack:latest
``` 
and only then run the application.

## Technological stack

- MySQL
- Hibernate
- Redis
- Docker