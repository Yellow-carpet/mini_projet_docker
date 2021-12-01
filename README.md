# mini_projet_docker


<div align="center">
<img src="https://jolicode.com/media/original/2013/10/homepage-docker-logo.png" width="500" height="450" />
</div>

Le but de ce projet est de réviser ce que nous avons vu sur [Docker](https://www.docker.com/) au cours de cette formation.

## Utilisation du docker file pour déployer 

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
docker run  -p 5000:5000 -v ${PWD}/student_age.json:/data/student_age.json -d --name=first_api student_image
```

