# 1. Image de base nginx
FROM nginx:latest

# 2. Supprimer la page par défaut de nginx
RUN rm -rf /usr/share/nginx/html/*

# 3. Copier le projet web dans nginx
COPY . /usr/share/nginx/html

# 4. Lancer nginx (commande imposée par l'énoncé)
CMD ["nginx", "-g", "daemon off;"]
