# TPs Girardin Mathis - Emile Mathias

## Q 1-1

Commandes effectuées :
```
## Builds the db image using db Dockerfile 
docker build -t bbc/db .

## Create network for communication between db and adminer
docker network create app-network

## Run docker images for DB part (adminer and db)
docker run --name db -e POSTGRES_PASSWORD=pwd -d --network app-network -v /home/tp/Documents/database/db:/var/lib/postgresql/data bbc/db

docker run --network app-network -d --name adminer -p 8080:8080 adminer
```

Dockerfile de l'image de la db :
```
## Dockerfile DB

***
#Import posgres image from 14.1 alpine
FROM postgres:14.1-alpine

#Define variables for postgres env
ENV POSTGRES_DB=db \
   POSTGRES_USER=usr \
   POSTGRES_PASSWORD=pwd

#Scripts to create and populate db
COPY CreateScheme.sql /docker-entrypoint-initdb.d/
COPY InsertData.sql /docker-entrypoint-initdb.d/
***

```

## Q 1-2

On a besoin du multistage build pour pouvoir build le fichier java avec maven directement dans le docker.

## Q 1-3/4

On ba build chaque services dans un docker différent, en ajoutants les options nécessaire au bon fonctionnement.

```
  backend:
    container_name: backend
    build: ./simple-api
    networks:
      - app-network
    depends_on:
      - database

  database:
    container_name: database
    restart: always
    build: ./database
    networks:
      - app-network
    env_file:
      - database/.env

  httpd:
    container_name: reverse_proxy
    build: ./httpd
    ports:
      - "80:80"
    networks:
      - app-network

```

Ainsi, dans le service backend on a le nom dudocker, le repertoire ciblé, le réseau utilisé et une instruction qui indique que le build de l'image du backend se fait après celle de la db.

Dans le service database on indique le réseau et le fichier des variables d'environnements.

Dans le httpd, on indique le port et le réseau.

Puis on créer le réseau avec :
```
networks:
  app-network:
 ```
 
## Q 1-5

Tout d'abord on se connecte avec:
```
docker login
```

Et ensuite, pour chaque image, on va créer un tag de l'image puis le push dans dockerhub :

```
docker tag my-database USERNAME/my-database:1.0

docker push USERNAME/my-database  

```

## Q 2-1

Les testcontainers sont des librairies java qui permettent de lancer le container docker en même temps que les tests de compilation du java.

## Q 2-2

Dans notre configuration, les Github Actions permettent qu'à chaque nouveau push on vérifie le build docker et que l'application se lance correctement. Tous celà est indiqué dans le fichier '.github/workflows/main.yml'. On y retrouve notamment les branches concernés, la version d'ubuntu et les étapes à réalisés:
   - Check out repository
   - Set up JDK 17
   - Build and test with Maven

## Q 2-3

Pour la configuration de notre Quality Gate on utilise SonarCloud. On renseigne les clés nécessaires dans le fichier '.github/workflows/main.yml' qui renvoie les infos suivantes lors des tests avec Maven. 

![image](https://user-images.githubusercontent.com/93118154/216331933-2fd05dc0-40ba-4866-a628-848c591c7a54.png)

## Q 3-1

On teste l'accès au serveur

![image](https://user-images.githubusercontent.com/93118154/216349424-c0bd8290-ac5a-4a02-8ecc-b68add42576e.png)

On envoie notre distribution au serveur pour qu'il se configure

![image](https://user-images.githubusercontent.com/93118154/216350212-491f202d-9fb1-496d-a59d-57cb5e506c71.png)

On enlève httpd du serveur

![image](https://user-images.githubusercontent.com/93118154/216350398-51414da9-ff2b-4587-be07-acbd52b372bb.png)

## Q 3-2

Notre playbook possède plusieurs taches qui installe des paquets sur le serveur centos.

   - Clean packages
   - Intsall device-mapper-persistent-data
   - Install lvm2
   - add repo docker
   - Install Docker
   - install python3
   - Pip install
   - Make sure Docker is running

## Q 3-3

TODO
