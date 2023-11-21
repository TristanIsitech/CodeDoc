Source : https://devopssec.fr/article/cours-complet-apprendre-technologie-docker#begin-article-section

# INTRO LINUX / CONTENEUR
## Les types de namespace Linux

- Le **namespace PID** : fournit un ensemble indépendant d'identifiants de processus (PID). Il s'agit d'une structure hiérarchique dans laquelle le namespace parent peut afficher tous les PID des namespaces enfants. Lorsqu'un nouveau namespace est créé, le premier processus obtient le PID 1 et constitue une sorte de processus init de ce namespace. Cela signifie également que si on tue ce processus PID 1 alors on mettra immédiatement fin à tous les processus de son namespace PID et à tous ses descendants.
- Le **namespace IPC** : empêche la communication avec les autres processus, plus simplement il interdit l'échange d'informations avec les autres processus.
- Le **namespace NET** : crée une pile réseau entièrement nouvelle, y compris : un ensemble privé d'adresses IP, sa propre table de routage, liste de socket, table de suivi des connexions, pare-feu et autres ressources liées au réseau.
- Le **namespace MOUNT** : monte un système de fichier propre au processus qui est différent du système de fichier de la machine hôte. Vous pouvez ainsi monter et démonter des systèmes de fichiers sans que cela n'affecte le système de fichiers hôte.
- Le **namespace USER** : fournit à la fois l'isolation des privilèges et la séparation des identifications d'utilisateurs entre plusieurs ensembles. Il permet par exemple de donner un accès root dans le conteneur sans qu'il soit root sur la machine hôte.
- Le **namespace UTS** : associe un nom d'hôte et de domaine au processus pour avoir son propre hostname.

## Les types de cgroup

- **cgroup cpuset** : assigne des processeurs individuels et des nœuds de mémoire à des groupes de contrôle
- **cgroup cpu** : planifie un accès aux ressources du processeur
- **cgroup cpuacct** : génère des rapports sur les ressources du processeur utilisées
- **cgroup devices** : autorise ou refuse l'accès aux périphériques
- **cgroup net_prio** : permet de définir la priorité du trafic réseau
- **cgroup memory** : définit la limite d'utilisation de la mémoire
- **cgroup blkio** : limite de la vitesse E/S (lecture/écriture) sur périphériques de type bloc (ex : disque dur)
- **cgroup pid** : limite le nombre de processus

# INSTALLATION DOCKER

Sur le site de docker suivre les informations d'installation et utilisé la commande suivante dans un PowerShell afin de tester si docker est correctement installé :
```SHELL
docker --version
```

# IMAGE DOCKER

## Définition

Une image docker est un package qui inclut tout ce qui est nécessaire à l'exécution d'une application, à savoir :
- Le code
- L'exécution
- Les variables d'environnement
- Les bibliothèques
- Les fichiers de configuration

## Les commandes de base

```SHELL
## Afficher de l'aide
docker help
docker <sous-commande> --help

## Afficher des informations sur l'installation de Docker
docker --version
docker version
docker info

## Lister des images Docker
docker image ls
# ou
docker images

## Supprimer une image Docker
docker images rmi <IMAGE_ID ou IMAGE_NAME>  # si c'est le nom de l'image qui est spécifié alors il prendra par défaut le tag latest
    -f ou --force : forcer la suppression

## Supprimer tous les images Docker
docker rmi -f $(docker images -q)

## Rechercher une image depuis le Docker hub Registry
docker search ubuntu
    --filter "is-official=true" : Afficher que les images officielles

## Télécharger une image depuis le Docker hub Registry
docker pull <IMAGE_NAME>  # prendra par défaut le tag latest
docker pull ubuntu:16.04 # prendra le tag 16.04
```

## Manipulation des conteneurs

Pour exécuter une image docker :
```BASH
docker run <IMAGE_NAME>
```

Dans la commande ``docker run`` on peut utiliser les option suivante :
- -t : **Allouer un pseudo TTY** (terminal virtuel)
- -i : **Garder un STDIN ouvert** (l'entrée standard plus précisément l'entrée clavier)
- -d : **Exécuter le conteneur en arrière-plan** et afficher l'ID du conteneur
- - --name : **Attribuer un nom au conteneur**
- --expose: **Exposer un port ou une plage de ports** (on demande au firewall du conteneur de nous ouvrir un port ou une plage de port)
- -p ou --publish: **Mapper un port** déjà exposé, plus simplement ça permet de **faire une redirection de port**
- -e : définit/surcharge des variables d'environnement
- --link : ajoute un lien à un autre conteneur afin de les faire communiquer entre eux.

On peut donc créer un conteneur et ouvrir un terminal a l'intérieur :
```SHELL
docker run -ti <IMAGE_NAME>
```

Pour quitter le terminal du conteneur :
```
Ctrl + P + Q
```

Afficher la liste des conteneurs :
```SHELL
docker container ls
# OU
docker ps

# UTILISER -a ou --all POUR TOUT AFFICHER
docker ps -a
```

Supprimer un conteneur :
```SHELL
docker rm <CONTAINER_NAME ou ID>
```

Exécuter une commande a l'intérieur d'un conteneur :
```SHELL
docker exec -ti <CONTAINER_NAME> /bin/bash
# ON PEUT MAINTENANT EXECUTER LES COMMANDES DANS LE CONTENEUR
```

Suivre les logs d'un conteneur :
```SHELL
docker logs -ft <CONTAINER_NAME>
# POUR QUITTER :
Ctrl + C
```
- -f : **suivre en permanence les logs du conteneur** (correspond à tail -f)
- -t : **afficher la date et l'heure de réception des logs d'un conteneur**

# CREER DES IMAGES DOCKER 

## Instruction Dockerfile courante

- **FROM** : Définit l'image de base qui sera utilisée par les instructions suivantes.
- **LABEL** : Ajoute des métadonnées à l'image avec un système de clés-valeurs, permet par exemple d'indiquer à l'utilisateur l'auteur du Dockerfile.
- **ARG** : Variables temporaires qu'on peut utiliser dans un Dockerfile.
- **ENV** : Variables d'environnements utilisables dans votre Dockerfile et conteneur.
- **RUN** : Exécute des commandes Linux ou Windows lors de la création de l'image. Chaque instruction RUN va créer une couche en cache qui sera réutilisée dans le cas de modification ultérieure du Dockerfile.
- **COPY** : Permet de copier des fichiers depuis notre machine locale vers le conteneur Docker.
- **ADD** : Même chose que COPY mais prend en charge des liens ou des archives (si le format est reconnu, alors il sera décompressé à la volée).
- **ENTRYPOINT** : comme son nom l'indique, c'est le point d'entrée de votre conteneur, en d'autres termes, c'est la commande qui sera toujours exécutée au démarrage du conteneur. Il prend la forme de tableau JSON (ex : CMD ["cmd1","cmd1"]) ou de texte.
- **CMD** : Spécifie les arguments qui seront envoyés au ENTRYPOINT, (on peut aussi l'utiliser pour lancer des commandes par défaut lors du démarrage d'un conteneur). Si il est utilisé pour fournir des arguments par défaut pour l'instruction ENTRYPOINT, alors les instructions CMD et ENTRYPOINT doivent être spécifiées au format de tableau JSON.
- **WORKDIR** : Définit le répertoire de travail qui sera utilisé pour le lancement des commandes CMD et/ou ENTRYPOINT et ça sera aussi le dossier courant lors du démarrage du conteneur.
- **EXPOSE** : Expose un port.
- **VOLUMES** : Crée un point de montage qui permettra de persister les données.
- **USER** : Désigne quel est l'utilisateur qui lancera les prochaines instructions RUN, CMD ou ENTRYPOINT (par défaut c'est l'utilisateur root).

# LES VOLUMES

Pour créer un volume :
```SHELL
docker volume create <VOLUMENAME>
```

Pour lister les volumes :
```SHELL
docker volume ls
```

Pour avoir des informations sur un volume :
```SHELL
docker volume inspect <VOLUMENAME>
```

Pour supprimer un volumes :
```SHELL
docker volume rm <VOLUMENAME>
```

Pour démarrer un conteneur avec un volume il faut utiliser la commande `-v` dans la commande `docker run`.

# DOCKER COMPOSE


```SHELL
## Exécuter les services du docker-compose.yml
docker-compose up
    -d : Exécuter les conteneurs en arrière-plan

## Lister des conteneurs du Docker Compose
docker-compose ls
    -a ou --all : afficher aussi les conteneurs stoppés

## Sorties/erreurs des conteneurs du Docker Compose
docker-compose logs
    -f # : suivre en permanence les logs du conteneur
    -t # : afficher la date et l'heure de la réception de la ligne de log
    --tail=<NOMBRE DE LIGNE> # = nombre de lignes à afficher à partir de la fin pour chaque conteneur.

## Tuer les conteneurs du Docker Compose
docker-compose kill

## Stopper les conteneurs du Docker Compose
docker-compose stop
    -t ou --timeout # : spécifier un timeout en seconde avant le stop (par défaut : 10s)

## Démarrer les conteneurs du Docker Compose
docker-compose start

## Arrêtez les conteneurs et supprimer les conteneurs, réseaux, volumes, et les images
docker-compose down
    -t ou --timeout # : spécifier un timeout en seconde avant la suppression (par défaut : 10s)

## Supprimer des conteneurs stoppés du Docker Compose
docker-compose rm
    -f ou --force # : forcer la suppression

## Lister les images utilisées dans le docker-compose.yml
docker-compose images
```

# DOCKER RESEAU

```SHELL
## Créer un réseau docker
docker network create --driver <DRIVER TYPE> <NETWORK NAME>

# Lister les réseaux docker
docker network ls

## Supprimer un ou plusieurs réseau(x) docker
docker network rm <NETWORK NAME>

## Récolter des informations sur un réseau docker
docker network inspect <NETWORK NAME>
    -v ou --verbose : mode verbose pour un meilleur diagnostique

## Supprimer tous les réseaux docker non inutilisés
docker network prune
    -f ou --force : forcer la suppression

## Connecter un conteneur à un réseau docker
docker network connect <NETWORK NAME> <CONTAINER NAME>

## Déconnecter un conteneur à réseau docker
docker network disconnect <NETWORK NAME> <CONTAINER NAME>
    -f ou --force : forcer la déconnexion

## Démarrer un conteneur et le connecter à un réseau docker
docker run --network <NETWORK NAME> <IMAGE NAME>
```






