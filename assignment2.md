### clean the old environment
```
docker stop $(docker ps -aq)
docker rm $(docker ps -aq)
docker rmi -f $(docker images -q)
docker network prune -f
docker volume prune -f
```

### folder structure
```
spring3hibernate/
├── Dockerfile
├── docker-compose.yml
├── src/
│   └── main/resources/database.properties
└── nginx/
    ├── Dockerfile
    └── default.conf
```

### Dockerfile
```
FROM maven:3.3-jdk-8 as builder
COPY . /usr/src/mymaven/
WORKDIR /usr/src/mymaven/
RUN mvn clean install -DskipTests -Ddependency-check.skip=true
RUN mvn package -DskipTests -Ddependency-check.skip=true

FROM tomcat:7-jre7-alpine
MAINTAINER "opstree <opstree@gmail.com>"
RUN rm -rf /usr/local/tomcat/webapps/*
COPY --from=builder /usr/src/mymaven/target/Spring3HibernateApp.war /usr/local/tomcat/webapps/ROOT.war
WORKDIR /usr/local/tomcat/webapps/
EXPOSE 8080
```

### vi src/main/resources/database.properties
```
database.driver=com.mysql.jdbc.Driver
database.url=jdbc:mysql://mysql.okts.tk:3306/employeedb
database.user=root
database.password=password
hibernate.dialect=org.hibernate.dialect.MySQLDialect
hibernate.show_sql=true
hibernate.hbm2ddl.auto=update
upload.dir=c:/uploads
```

### docker-compose.yml
```
version: '3'
services:
  spring3hibernate.okts.tk:
    build:
      context: . 
      dockerfile: Dockerfile
    expose:
      - '8080'
    restart: always
    depends_on: 
      - mysql.okts.tk

  mysql.okts.tk:
    image: mysql:5.6
    restart: always
    expose:
      - '3306'
    environment:
       MYSQL_DATABASE: employeedb
       MYSQL_ROOT_PASSWORD: password
    volumes:
       - mysql_okts_tk:/var/lib/mysql

  ingress.okts.tk:
    build:
      context: nginx
      dockerfile: Dockerfile
    ports:
      - "80:80" 
    restart: always
    depends_on: 
      - spring3hibernate.okts.tk

volumes:
  mysql_okts_tk:
```

### nginx/Dockerfile
### nginx/default.conf

### docker compose to stop, build, run 
```
docker-compose down -v
docker-compose build
docker-compose up -d
```

### verify
```
docker ps
```

### verify database established from springhibernate app
```
docker logs -f spring3hibernate.okts.tk         #Hibernate “Connected” message
docker exec -it spring3hibernate.okts.tk bash
ping mysql.okts.tk                              #ping my-sql container id
apk update && apk add mysql-client              #apt-get update && apt-get install -y mysql-client
mysql -h mysql.okts.tk -u root -ppassword -e "SHOW DATABASES;"
```



### Images
<img width="638" height="356" alt="image" src="https://github.com/user-attachments/assets/806b653d-8982-45f1-b503-840e71ca62bf" />


### checklist
```
| Step | Check                                 | Command                                                          | Expected Result               |
| ---- | ------------------------------------- | ---------------------------------------------------------------- | ----------------------------- |
| 1    | MySQL container running               | `docker ps`                                                      | mysql.okts.tk listed          |
| 2    | Ping MySQL                            | `ping mysql.okts.tk`                                             | replies                       |
| 3    | Test MySQL connection                 | `mysql -h mysql.okts.tk -u root -ppassword -e "SHOW DATABASES;"` | `employeedb` listed           |
| 4    | App logs confirm Hibernate connection | `docker logs -f spring3hibernate.okts.tk`                        | Hibernate “Connected” message |
```

### optional cleanup
```
docker-compose down -v
docker system prune -af
docker-compose build
docker-compose up -d
```
