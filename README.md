# Projet Docker - E-Commerce Microservices

Projet réalisé par Florent MENUS et Tom DEPUSSAY.

## 1 Construire et exécuter les conteneurs

### Prérequis
- Docker
- Docker Compose

### Démarrage en développement
Depuis le dossier e-commerce-vue-main :

~~~bash
cd e-commerce-vue-main
docker compose up --build
~~~

Le frontend est ensuite accessible sur http://localhost:8080.

### Démarrage en production (simulation locale)
Depuis le dossier e-commerce-vue-main :

~~~bash
cd e-commerce-vue-main
docker compose -f docker-compose.prod.yml up --build -d
~~~

### Arrêt et nettoyage
~~~bash
docker compose down
docker compose -f docker-compose.prod.yml down
~~~

Si vous souhaitez aussi supprimer les volumes MongoDB :

~~~bash
docker compose down -v
docker compose -f docker-compose.prod.yml down -v
~~~

## 2 Configurations spécifiques par environnement

### Fichiers de variables d'environnement
- Racine projet : .env, .env.development, .env.production
- Frontend : frontend/.env, frontend/.env.production
- Services backend : services/auth-service/.env, services/order-service/.env, services/product-service/.env (et variantes .env.production)

### Développement
- Fichier d'orchestration : docker-compose.yml
- Objectif : itération rapide
- Caractéristiques :
	- Montage des sources en volumes (hot reload côté services/frontend)
	- Variables NODE_ENV=development
	- Dépendances installées dans les conteneurs
	- Bases MongoDB séparées par service

### Production
- Fichier d'orchestration : docker-compose.prod.yml
- Objectif : exécution stabilisée
- Caractéristiques :
	- Variables NODE_ENV=production
	- Images taggées (REGISTRY / IMAGE_TAG)
	- Politiques de redémarrage (restart_policy)
	- Rotation des logs (max-size, max-file)
	- Configuration deploy (compatible Docker Swarm)

### Variables importantes
- JWT_SECRET : secret de signature des tokens JWT
- VITE_AUTH_SERVICE_URL : URL du service auth
- VITE_PRODUCT_SERVICE_URL : URL du service produits
- VITE_ORDER_SERVICE_URL : URL du service commandes

## 3 Commandes pour tester les services

### Lancer toute la suite de tests
Depuis e-commerce-vue-main :

~~~bash
chmod +x scripts/run-tests.sh
./scripts/run-tests.sh
~~~

### Vérification de l'état des conteneurs
~~~bash
docker compose ps
docker compose -f docker-compose.prod.yml ps
~~~

## 4 Bonnes pratiques Docker appliquées

- Architecture microservices : séparation frontend, auth, product, order et bases Mongo dédiées.
- Réseau Docker dédié : communication interne via ecommerce-network.
- Volumes nommés : persistance des données MongoDB.
- Healthchecks : vérification active de la disponibilité MongoDB et des services.
- Démarrage orchestré : depends_on avec conditions de santé.
- Sécurité des conteneurs : exécution en utilisateur non-root dans les Dockerfiles Node/Nginx.
- Images optimisées : build multi-stage pour limiter la taille des images de production.
- Fiabilité en production : restart policy et stratégie d'update déclarées.
- Observabilité minimale : rotation des logs pour éviter la saturation disque.
- Initialisation contrôlée : service init-products pour injecter les produits au démarrage.

## 5 Commandes utiles

Voir les logs :

~~~bash
docker compose logs -f
docker compose -f docker-compose.prod.yml logs -f
~~~

Rebuild d'un seul service (exemple product-service) :

~~~bash
docker compose up --build product-service
~~~

