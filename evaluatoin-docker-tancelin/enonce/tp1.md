# TP1 : Manipulation des Conteneurs

## Contexte

Vous venez d'être embauché comme DevOps junior dans une startup. Votre première mission est de démontrer votre maîtrise des commandes Docker de base en manipulant des conteneurs.

---

## Exercice 1 : Premiers pas

### 1.1 Vérification de l'installation 

Affichez la version de Docker installée et notez-la dans votre fichier de réponses.

**Commande attendue :**

```bash
docker --version
```

### 1.2 Téléchargement d'images 

Téléchargez les images suivantes :
- `nginx:alpine`
- `redis:7-alpine`

**Questions :**

- Quelle est la taille de chaque image ?

```
la nginx fait  93.4, la redis 61.2

pour les savoir j'ai fait
docker pull nginx:alpine  
docker pull redis:7-alpine
docker images et regarder la taille

```

- Pourquoi utilise-t-on des images `alpine` ?
```
car les image apline sont une distrib linux basique, avec peut de package, pas lourde, téléchargeable rapidement
```
### 1.3 Liste des images 

Affichez la liste de toutes les images présentes sur votre système.

**Questions :**

- Quelle commande avez-vous utilisée ?
```
j'ai utilisée 

docker images
```
- Combien d'images sont présentes ?

```
j'en ait 10, je l'ai trouvée grace a 

docker images | Measure-Object
(valable sous windows)

```

---

## Exercice 2 : Gestion des conteneurs 

### 2.1 Lancer un conteneur Nginx 

Lancez un conteneur Nginx avec les caractéristiques suivantes :
- Nom : `web-eval`
- Mode détaché
- Port 8080 de l'hôte mappé vers le port 80 du conteneur

**Vérification :** Accédez à `http://localhost:8080` dans votre navigateur.
```
docker run -d --name web-eval -p 8080:80 nginx:alpine

vérification sur localhost:8080

```
### 2.2 Inspection du conteneur 

Répondez aux questions suivantes sur le conteneur `web-eval` :
- Quelle est son adresse IP ?
```
docker inspect -f "{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}" web-eval
172.17.0.2
```
- Quel est son état (status) ?
```
docker inspect -f "{{ .State.Status }}" web-eval
running
```
- Quand a-t-il été créé ?
```
docker inspect -f "{{ .Created }}" web-eval
2026-02-09T09:24:59.694760575Z
```

### 2.3 Logs et processus 

- Affichez les 10 dernières lignes de logs du conteneur
```
docker logs --tail 10 web-eval

```
- Affichez les processus en cours d'exécution dans le conteneur
```
docker top web-eval
```
### 2.4 Exécution de commandes 

Exécutez les actions suivantes dans le conteneur `web-eval` :
1. Ouvrez un shell interactif
```
docker exec -it web-eval sh

```
2. Créez un fichier `/tmp/evaluation.txt` contenant votre nom
```
echo "Tancelin" > /tmp/evaluation.txt

```
3. Vérifiez que le fichier existe
```
cat /tmp/evaluation.txt

```
4. Quittez le shell

```
exit

```
---

## Exercice 3 : Cycle de vie 

### 3.1 Arrêt et redémarrage 

- Arrêtez le conteneur `web-eval`
```
docker stop web-eval
```
- Vérifiez qu'il est bien arrêté
```
docker ps -a
```
- Redémarrez-le
```
docker start web-eval

```
- Vérifiez que le fichier `/tmp/evaluation.txt` existe toujours
```
docker exec -it web-eval cat /tmp/evaluation.txt
```
**Question :** Le fichier existe-t-il toujours ? Pourquoi ?

```
il existe toujours, car la commande et juste coupée et redémarrée, ont n'a pas supprimée le container
```

### 3.2 Création d'un conteneur Redis 

Lancez un conteneur Redis avec :
- Nom : `cache-eval`
- Mode détaché
- Pas de mapping de port

Connectez-vous au CLI Redis et exécutez :

```
docker run -d --name cache-eval redis:7-alpine
docker exec -it cache-eval redis-cli

SET evaluation "reussie"
GET evaluation
```

### 3.3 Gestion multiple 

- Listez tous les conteneurs (actifs et inactifs)
```
docker ps -a
```
- Arrêtez tous les conteneurs en une seule commande
```
docker stop $(docker ps -aq)

```
- Supprimez tous les conteneurs arrêtés en une seule commande
```
docker rm $(docker ps -aq -f status=exited)

```

**Questions :**
- Quelles commandes avez-vous utilisées ?
```

```
- Quelle est la différence entre `docker stop` et `docker rm` ?
```
la différence et que stop le met en "arret" et rm le supprime
sa signifie que lorsqu'il est en "arret" il existe encore dans le système, alors que stop le supprime purement et simplement (ces modification apportée également)
```
---

## Exercice 4 : Volumes et persistance 

### 4.1 Création d'un volume 
Créez un volume Docker nommé `data-eval`.
```
docker volume create data-eval

ont peut vérifiée avec 

docker volume ls

```
### 4.2 Utilisation du volume 

Lancez un conteneur `alpine` qui :
- Monte le volume `data-eval` sur `/data`
```
docker run -it --name alpine-eval -v data-eval:/data alpine sh

```
- Crée un fichier `/data/persistant.txt` avec du contenu
```
echo "Mes données persistantes" > /data/persistant.txt

ls /data


```
- Se termine après exécution
```
exit

```
### 4.3 Vérification de la persistance 

- Lancez un nouveau conteneur `alpine` montant le même volume
```
docker run -it --rm -v data-eval:/data alpine sh

```
- Vérifiez que le fichier `persistant.txt` existe et contient les données
```
ls /data

cat /data/persistant.txt

exit

```
**Question :** Expliquez pourquoi les données persistent entre les conteneurs.
```
les volume et les container sont indépendant, malgré la suppression du containeur d'origine ou la donée a était créer, le volume restant intact, a pus récupérée ces donnée et les transmettre aux container suivant utilisant le même volume
```
---

## Nettoyage

À la fin du TP, nettoyez votre environnement :

```bash

# Supprimez tous les conteneurs créés
docker rm -f $(docker ps -aq)

# Supprimez le volume créé
docker volume rm data-eval

# Conservez les images pour les TPs suivants
docker images

```

