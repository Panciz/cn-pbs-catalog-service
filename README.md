# Catalog Service

This application is part of the Polar Bookshop system and provides the functionality for managing
the books in the bookshop catalog. It's part of the project built in the
[Cloud Native Spring in Action](https://www.manning.com/books/cloud-native-spring-in-action) book
by [Thomas Vitale](https://www.thomasvitale.com).

## REST API

| Endpoint	      | Method   | Req. body  | Status | Resp. body     | Description    		   	     |
|:---------------:|:--------:|:----------:|:------:|:--------------:|:-------------------------------|
| `/books`        | `GET`    |            | 200    | Book[]         | Get all the books in the catalog. |
| `/books`        | `POST`   | Book       | 201    | Book           | Add a new book to the catalog. |
|                 |          |            | 422    |                | A book with the same ISBN already exists. |
| `/books/{isbn}` | `GET`    |            | 200    | Book           | Get the book with the given ISBN. |
|                 |          |            | 404    |                | No book with the given ISBN exists. |
| `/books/{isbn}` | `PUT`    | Book       | 200    | Book           | Update the book with the given ISBN. |
|                 |          |            | 201    | Book           | Create a book with the given ISBN. |
| `/books/{isbn}` | `DELETE` |            | 204    |                | Delete the book with the given ISBN. |

## Useful Commands

| Gradle Command	         | Description                                   |
|:---------------------------|:----------------------------------------------|
| `./gradlew bootRun`        | Run the application.                          |
| `./gradlew build`          | Build the application.                        |
| `./gradlew test`           | Run tests.                                    |
| `./gradlew bootJar`        | Package the application as a JAR.             |
| `./gradlew bootBuildImage` | Package the application as a container image. |

After building the application, you can also run it from the Java CLI:

```bash
java -jar build/libs/catalog-service-0.0.3-SNAPSHOT.jar
```

## Container tasks

Run Catalog Service as a container

```bash
docker run --rm --name catalog-service -p 8080:9001 catalog-service:0.0.3-SNAPSHOT
```

### Container Commands

| Docker Command	              | Description       |
|:-------------------------------:|:-----------------:|
| `docker stop catalog-service`   | Stop container.   |
| `docker start catalog-service`  | Start container.  |
| `docker remove catalog-service` | Remove container. |




### Run Only Database

Run PostgreSQL as a Docker container

```bash
docker compose -f docker/docker-compose-db-only.yml up -d
```


Start an interactive PSQL console:

```bash
docker exec -it polar-postgres psql -U user -d polardb_catalog
```

| PSQL Command	              | Description                                    |
|:---------------------------|:-----------------------------------------------|
| `\list`                    | List all databases.                            |
| `\connect polardb_catalog` | Connect to specific database.                  |
| `\dt`                      | List all tables.                               |
| `\d book`                  | Show the `book` table schema.                  |
| `\d flyway_schema_history` | Show the `flyway_schema_history` table schema. |
| `\quit`                    | Quit interactive psql console.                 |

From within the PSQL console, you can also fetch all the data stored in the `book` table.

```bash
select * from book;
```

The following query is to fetch all the data stored in the `flyway_schema_history` table.

```bash
select * from flyway_schema_history;
```

## Kubernetes tasks

Lanciare `./gradlew bootBuildImage` su tutti i progetti
(ignorare ` saving image: fetching base layers: unexpected EOF` )

```
 k apply -f kubernetes/platform/development/services/postgresql.yml 
 k apply -f kubernetes/platform/development/services/redis.yml 

 k apply -f k8s/deployment.yml      
 k apply -f k8s/service.yml      
 k apply -f ../config-service/k8s/deployment.yml 
 k apply -f ../config-service/k8s/service.yml
 k apply -f ../order-service/k8s/deployment.yml 
 k apply -f ../order-service/k8s/service.yml
 k apply -f ../edge-service/k8s/deployment.yml 
 k apply -f ../edge-service/k8s/service.yml 
 ```



to access from the external

With ingress (vedi postman env `CN Polar Bookshow Local k8 ingress.postman_environment`)

```
helm upgrade --install ingress-nginx ingress-nginx \
  --repo https://kubernetes.github.io/ingress-nginx \
  --namespace ingress-nginx --create-namespace
 
 k apply -f ../edge-service/k8s/ingress.yml 
```

Alternatevly With port forwarding `CN Polar Bookshow Local k8.postman_environment`
 
```
kubectl port-forward svc/catalog-service 8080:80 & \
kubectl port-forward svc/order-service 8081:80 &
```

to get status

```
$ k get all,ingress -l platform=polar-catalog
```
