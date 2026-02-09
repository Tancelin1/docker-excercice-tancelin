# TP2 : Dockerfile et Construction d'Images


## Contexte

L'équipe de développement a créé une application web simple. Votre mission est de la conteneuriser en créant des Dockerfiles optimisés et en maîtrisant le processus de build.

---

## Exercice 1 : Dockerfile basique 

### 1.1 Application Node.js 

Créez un Dockerfile pour une application Node.js avec les spécifications suivantes :


**Fichier `package.json` à créer :**


**Exigences du Dockerfile :**
- Image de base : `node:18-alpine`
- Répertoire de travail : `/app`
- Copier les fichiers de l'application
- Exposer le port 3000
- Commande de démarrage : `npm start`

```
FROM node:18-alpine
WORKDIR /app

COPY package.json ./
COPY app.js ./

EXPOSE 3000

CMD ["npm", "start"]
```
### 1.2 Build et test 

- Construisez l'image avec le tag `eval-app:v1`
```
docker build -t eval-app:v1 ./node-app
```
- Lancez un conteneur sur le port 3001
```
docker run -d -p 3001:3000 --name eval-app-v1 eval-app:v1
```
- Testez avec `curl http://localhost:3001`
```
StatusCode        : 200
StatusDescription : OK
Content           : {"message":"Hello from Docker!","timestamp":"2026-02-09T10:52:00.539Z","hostname":"d82957aae23b"}
RawContent        : HTTP/1.1 200 OK
                    Connection: keep-alive
                    Keep-Alive: timeout=5
                    Transfer-Encoding: chunked
                    Content-Type: application/json
                    Date: Mon, 09 Feb 2026 10:52:00 GMT


```
- Notez la taille de l'image
```
docker images eval-app:v1

44.9MB
```


**Questions :**
- Combien de layers l'image contient-elle ?
```
docker history eval-app:v1
il y en a 14 pour moi
```
- Quelle commande permet de voir l'historique des layers ?
```
docker history eval-app:v1

```

---

## Exercice 2 : Optimisation du Dockerfile 

### 2.1 Cache des dépendances 

Modifiez le Dockerfile pour optimiser le cache de build :
- Copiez d'abord `package.json` seul
- Installez les dépendances avec `npm install`
- Puis copiez le reste du code
```
FROM node:18-alpine
WORKDIR /app

COPY package.json ./

RUN npm install

COPY app.js ./

EXPOSE 3000
CMD ["npm", "start"]

```

**Question :** Expliquez pourquoi cette approche est plus efficace.

### 2.2 Multi-stage build 

Créez un nouveau Dockerfile utilisant un build multi-stage pour une application avec dépendances de développement :

**Modifiez `package.json` :**


**Exigences :**
- Stage 1 (`builder`) : Installation de toutes les dépendances et tests
```
FROM node:18-alpine AS builder
WORKDIR /app

COPY package.json ./
RUN npm install

COPY app.js ./


RUN npm run test
```
- Stage 2 (`production`) : Uniquement les dépendances de production
```
FROM node:18-alpine AS production
WORKDIR /app

COPY --from=builder /app/node_modules ./node_modules
COPY --from=builder /app/app.js ./

EXPOSE 3000
CMD ["npm", "start"]

```

Construisez avec le tag `eval-app:v2` et comparez la taille avec v1.
```
docker build -t eval-app:v1-optimized .

docker build -t eval-app:v2 -f Dockerfile.multistage .
46.2 la v1
45.2 la v2
```

### 2.3 Utilisateur non-root 

Modifiez le Dockerfile pour :
- Créer un utilisateur `appuser` avec UID 1001
```
RUN adduser -D -u 1001 appuser

COPY --from=builder /app/node_modules ./node_modules
COPY --from=builder /app/app.js ./

USER appuser

EXPOSE 3000
CMD ["npm", "start"]
```
- Exécuter l'application avec cet utilisateur
```
docker build -t eval-app:v2-nonroot -f Dockerfile.multistage .
```

**Question :** Pourquoi est-ce important pour la sécurité ?
```
des faille existe pour prendre le controle de la machine hote, créer un compte permet de limitée les accès a des fichier sensible et limitée les commande autorisée.
```

---

## Exercice 3 : Arguments et variables 

### 3.1 ARG et ENV 

Créez un Dockerfile paramétrable avec :
- `ARG NODE_VERSION=18` pour la version de Node
```
ARG NODE_VERSION=18
```
- `ENV APP_ENV=production` pour l'environnement
```
ENV APP_ENV=production
```
- `ENV PORT=3000` pour le port
```
ENV PORT=3000
```

Modifiez `app.js` pour afficher l'environnement :



### 3.2 Build avec arguments 

- Construisez l'image avec `NODE_VERSION=20` : tag `eval-app:v3-node20`
```
docker build --build-arg NODE_VERSION=20 -t eval-app:v3-node20 .

```
- Lancez un conteneur en surchargeant `APP_ENV=development`
```
docker run -d -p 3003:3000 -e APP_ENV=development --name eval-app-v3 eval-app:v3-node20

```
- Vérifiez que la variable est bien prise en compte
```
curl http://localhost:3003

```

**Questions :**
- Quelle est la différence entre ARG et ENV ?
```
arg est possible que en build, et ne persiste pas après exécution alors que env  et disponible dans le container  et il persiste après exécution
```
- Comment passer une variable d'environnement au `docker run` ?
```
docker run -d -p 3003:3000 -e APP_ENV=development --name eval-app-v3 eval-app:v3-node20

```

---

## Exercice 4 : Application Python 

### 4.1 Dockerfile Python 

Créez un Dockerfile pour l'application Flask suivante :


**Exigences :**

- Image de base : `python:3.11-slim`
- Utiliser gunicorn en production : `gunicorn --bind 0.0.0.0:5000 app:app`
- Optimisation du cache pip
```
FROM python:3.11-slim

WORKDIR /app

COPY requirements.txt ./
RUN pip install --no-cache-dir -r requirements.txt

COPY app.py ./

EXPOSE 5000

CMD ["gunicorn", "--bind", "0.0.0.0:5000", "app:app"]

```
### 4.2 HEALTHCHECK 

Ajoutez une instruction HEALTHCHECK au Dockerfile :
- Intervalle : 30 secondes
```
HEALTHCHECK \
  --interval=30s \

```
- Timeout : 10 secondes
```
HEALTHCHECK \
  --timeout=10s \
```
- Retries : 3
```
HEALTHCHECK \
  --retries=3 \
```
- Endpoint : `/health`
```
CMD curl -f http://localhost:5000/health || exit 1
```

Construisez avec le tag `eval-flask:v1` et vérifiez le healthcheck.

---

## Livrables attendus

```
tp2/
├── node-app/
│   ├── Dockerfile
│   ├── Dockerfile.multistage
│   ├── app.js
│   └── package.json
├── python-app/
│   ├── Dockerfile
│   ├── app.py
│   └── requirements.txt
└── reponses.md
```

