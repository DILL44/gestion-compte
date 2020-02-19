# Installation

## Prérequis
## Les dépôts
```
git clone git@gitlab.com:DILL44/dev-docker-symfony.git
git clone git@github.com:DILL44/gestion-compte.git
```
## Les container dockers

dans le répertoire dev-docker-symfony (`cd dev-docker-symfony`)
```
git checkout php7.3-mariadb10.4
```

copier le `.env.dist` en `.env` et éditer le
```
DIRECTORY_PROJECT=../gestion-compte
COMPOSE_PROJECT_NAME=scopeli
START_IP=172.42
UID=1000
GID=1000
```
le `../gestion-compte` correspond au chemin relatif du projet Symfony  
`scopeli` et `42` peux être remplacer par n'importe quoi, qui n'est pas utiliser par un autre clone du même dépôts  
sous GNU/Linux, UID et GUI peuvent être trouver par `id -u` et `id -g`  

démarrer les containers
```
docker-compose up -d --build
```

### Le déploiement de l'application gestion des membres

`docker-compose exec dev_symfony bash`

installer le vendor:
```
composer install
```

configuration en fin de composer (les entrées ou il faut être vigilent) :
```
database_host (127.0.0.1): mysql_symfony
database_name (symfony): db
database_password (null): root
super_admin.username (admin):    
mailer_host (127.0.0.1): mailcatcher_symfony
mailer_port (25): 1050
router.request_context.scheme (https): http
project_url ('https://yourcoop.local/'): http://yourcoop.local
place_local_ip_address ('127.0.0.1,192.168.0.x'): 172.42.0.x
```

tous ces paramètres peuvent être modifier dans `app/config/parameters.yml`

Installation de la base de données
```
sf doctrine:migration:migrate
```
Installation des images
```
sf assetic:dump
```

### Dernière configuration
Éditer le fichier /etc/hosts et ajouter:
```
172.42.0.25 membres.yourcoop.local
```
et dans le container de dev
```
chmod -R 777 var/
```

### accéder à mail catcher et à phpmyadmin

* pour trouver l'ip de phpmyadmin
```
docker inspect scopeli_phpmyadmin_symfony_1 | grep IPA
```
donne `"IPAddress": "172.24.0.5",`  
donc dans le navigateur http://172.24.0.5

* pour trouver l'url de mail catcher
```
docker inspect scopeli_phpmyadmin_symfony_1 | grep IPA
```
donne `"IPAddress": "172.24.0.26",`  
donc dans le navigateur http://172.24.0.5:1080
