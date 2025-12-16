# TP conteneur

## Partie 1

- En utilisant votre machine Windows, lancez le service Docker, s’il n’est pas lancé.

- Créer une image Docker sur votre machine du jeu 2048 (voir screen jeux_2048).
```bash 
docker pull fanvinga/docker-2048:latest
```
- Vérifier que l’image est bien présente sur votre machine.
```bash 
docker images
```
- Lancer ce jeu sur un port disponible au travers d’un conteneur que vous allez appeler «jeu-votre-nom ». 
```bash
docker run -d -p 8080:80 --name jeu-tancelin fanvinga/docker-2048:latest
```
- Vérifier que le conteneur est bien lancé avec la commande adaptée.
```bash 
docker ps
```
- Créer un second conteneur qui va lancer le même jeu mais avec un nom différent «jeu2-votre-nom ».
```bash 
docker run -d -p 8081:80 --name jeu2-tancelin fanvinga/docker-2048:latest
```
- Les 2 jeux sont fonctionnels en même temps sur votre machine, effectuez la commande pour vérifier la présence des conteneurs.
```bash 
docker ps
```
- Ouvrez les 2 jeux sur votre navigateur. 
```bash 
http://localhost:8080
http://localhost:8081
```
- Stopper les 2 conteneurs et assurez-vous que ces 2 conteneurs sont arrêtés.
```bash 
docker stop jeu-tancelin
docker stop jeu2-tancelin
docker ps -a

```
- Relancez le conteneur «jeu2-votre-nom » et aller vérifier dans votre navigateur s’il fonctionne bien. Effectuez la commande pour voir s’il a bien été relancé. Puis stopper le. 
```bash 
docker start jeu2-tancelin
docker ps
http://localhost:8081
docker stop jeu2-tancelin

```
- Supprimez l’image du jeu 2048 et les conteneurs associés.
```bash 
docker rm jeu-tancelin
docker rm jeu2-tancelin
docker rmi fanvinga/docker-2048:latest
```
- Vérifiez que les suppressions ont bien été faite.
```bash 
docker ps -a
docker images
```

## Partie 2


- Récupérer une image docker nginx.
```bash 

```

- Créer un conteneur en vous basant sur cette image en lui attribuant le nom suivant : « nginx-web».
```bash 
docker run -d -p 8080:80 --name nginx-web nginx:latest
```
- Assurez-vous que l’image est bien présente et que le conteneur est bien lancé.
```bash 
docker images
docker ps
```
- Ce serveur nginx web (nginx-web) devra être lancé sur un port disponible.
```bash 
docker ps
```
- Vérifier que le serveur est bien lancé au travers du navigateur.
```bash 
localhost:8080
```
- Une page web avec «Welcome to nignx » devrait s'afficher (voir nginx.png). 
```bash 
http://localhost:8080
```
- Effectuer la commande vous permettant de rentrer à l’intérieur de votre serveur nginx.
```bash 
docker exec -it nginx-web bash
```
- Une fois à l’intérieur, aller modifier la page html par défaut de votre serveur nginx en changeant le titre de la page en :  
Welcome «votre prenom ».
```bash 
docker exec -it nginx-web sh -c 'echo "<!DOCTYPE html><html><head><title>Welcome Tancelin</title></head><body><h1>Welcome Tancelin</h1></body></html>" > /usr/share/nginx/html/index.html'
```
- Relancez votre serveur et assurez-vous que le changement à bien été pris en compte, en relançant votre navigateur.
```bash 
docker restart nginx-web
http://localhost:8080

```
- Refaite la même opération mais en utilisant le serveur web apache et donc il faudra créer un autre conteneur.
```bash 
docker run -d -p 8081:80 --name apache-web httpd:latest
```
- Il faut supprimer le contenu complet de l'index.html et y mettre : "Je suis heureux et je m'appelle votre prenom".
```bash 
docker exec -it apache-web sh -c 'echo "<!DOCTYPE html><html><head><title>Je suis heureux</title></head><body>Im happy and my name is Tancelin</body></html>" > /usr/local/apache2/htdocs/index.html'
```
- Le changement doit appaître dans votre navigateur.
```bash 
docker restart apache-web
http://localhost:8081
```
## Partie 3


- Répétez 3 fois la même opération que pour le début de la partie 2, il faudra juste appelez vos conteneurs :

- « nginx-web3 ».

- « nginx-web4 ».

- « nginx-web5 ».

- Il faudra faire en sorte que les pages html présente dans les fichiers ci-dessous s’affiche dans chacun des navigateurs en lien avec vos conteneurs :

- html5up-editorial-m2i.zip pour nginx-web3

- html5up-massively.zip pour nginx-web4

- html5up-paradigm-shift.zip pour nginx-web5

- Stopper, ensuite, ces différents conteneurs.
