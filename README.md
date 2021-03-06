# mini_projet_docker


<div align="center">
<img src="https://jolicode.com/media/original/2013/10/homepage-docker-logo.png" width="500" height="450" />
</div>

Le but de ce projet est de réviser ce que nous avons vu sur [Docker](https://www.docker.com/) au cours de cette formation.

## Utilisation du dockerfile pour déployer 

Pour répondre à la première question, il suffisait de créer le fichier docker file comme suit :

```
# On importe l'image de python
FROM python:2.7-stretch 

#On créer le maintainer
LABEL maintainer='DJEMA SOFIANE' 

#On fait les différentes mise à jours et teléléchargement des paquets
RUN  apt-get update -y && apt-get install python-dev python3-dev libsasl2-dev python-dev libldap2-dev libssl-dev -y 
RUN pip install flask==1.1.2 flask_httpauth==4.1.0 flask_simpleldap python-dotenv==0.14.0

# Rajout d'un volume 
VOLUME ["/data"]

# Copie du fichier python
COPY student_age.py /

# Nous exposons en 5000
EXPOSE 5000

# Et enfin on précise la commande qui se lance
CMD [ "python", "./student_age.py" ]

```
Après cela on crée l'image docker à partir du fichier Dockerfile. Ce dernier est localisé dans le dossier "simple_api".
```
docker build -t student_image .
```
"student_image" represente le nom de notre image et, le "." l'emplacement de notre dockerfile (c'est-à-dire le répértoire courant).

Une fois l'image crée, nous n'avons plus qu'à crée notre conteneur.
```
docker run -d -p 5000:5000 -v ${PWD}/student_age.json:/data/student_age.json --name=first_api student_image
```
Voici le résultat à l'écran :
<div align="center">
<img src="images\container_run.PNG" width="800" height="40" />
</div>


Donc pour expliquer la commande :
 * -d : c'est pour le run en background
 * -p : c'est le port
 * -v : c'est pour attacher (mount) le fichier "student_age.json" dans le repertoire "data" du conteneur
 * --name : represente le nom du conteneur
 * student_image : le nom de l'image qui est utilsé pour crée notre conteneur.

Quand je lance la commande suivante :

```
curl -I toto:python -X GET http://localhost:5000/pozos/api/v1.0/get_student_ages
```
J'ai ce résultat :

<div align="center">
<img src="images\curl.PNG" width="500" height="60" />
</div>

Cette erreur d'authentification est sûrement dû à un mauvais username.


## Utilisation du docker-compose pour déployer 

Maintenant passons au docker-compose. Voici un aperçu du fichier docker-compose.yaml :
```
version: "3.3"
services:
  API:
    container_name: first_api
    image: student_image
    ports:
      - "5000:5000"
    volumes:
      - "${PWD}/student_age.json:/data/student_age.json"
  website:
    container_name: website
    depends_on:
      - API
    environment:
      - PASSWORD=root
      - USERNAME=toto
    image: "php:apache"
    ports:
      - "80:80"
    volumes:
      - "./website:/var/www/html"

```
Pour le lancer :
```
docker-compose up -d 
```
<div align="center">
<img src="images\up.PNG" width="500" height="60" />
</div>

Maintenant si on fait un petit tour sur le site :
<div align="center">
<img src="images\site1.PNG" width="700" height="500" />
</div>

