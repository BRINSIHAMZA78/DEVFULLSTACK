# TD - Comprendre les API REST et les Microservices

## 📚 Table des matières
1. [Introduction aux API REST](#introduction)
2. [Architecture Microservices](#architecture)
3. [Concepts clés](#concepts)
4. [Exercice 1 : Consommer des données depuis un fichier JSON](#exercice-1)
5. [Exercice 2 : Consommer une API publique](#exercice-2)

---

## 🎯 Introduction aux API REST {#introduction}

### Qu'est-ce qu'une API REST ?

**REST** (Representational State Transfer) est un style d'architecture pour les services web qui utilise le protocole HTTP.

**Principes de base :**
- **Client-Serveur** : Séparation des responsabilités
- **Sans état (Stateless)** : Chaque requête est indépendante
- **Cacheable** : Les réponses peuvent être mises en cache
- **Interface uniforme** : Utilisation des méthodes HTTP standard

### Les méthodes HTTP

| Méthode | Action | Exemple |
|---------|--------|---------|
| **GET** | Récupérer des données | `GET /api/users` |
| **POST** | Créer une ressource | `POST /api/users` |
| **PUT** | Modifier une ressource complète | `PUT /api/users/1` |
| **PATCH** | Modifier partiellement | `PATCH /api/users/1` |
| **DELETE** | Supprimer une ressource | `DELETE /api/users/1` |

### Les codes de statut HTTP

| Code | Signification | Exemple |
|------|---------------|---------|
| **200** | OK - Succès | Données récupérées avec succès |
| **201** | Created - Créé | Nouvelle ressource créée |
| **400** | Bad Request - Mauvaise requête | Données invalides |
| **404** | Not Found - Non trouvé | Ressource inexistante |
| **500** | Internal Server Error | Erreur serveur |

---

## 🏗️ Architecture Microservices {#architecture}

### Schéma d'une Application Microservices

```
┌─────────────────────────────────────────────────────────────────┐
│                         CLIENT (Navigateur)                      │
│                    Interface Utilisateur (UI)                    │
└───────────────────────────────┬─────────────────────────────────┘
                                │
                                │ HTTP/HTTPS
                                ▼
┌─────────────────────────────────────────────────────────────────┐
│                          API GATEWAY                             │
│         (Point d'entrée unique - Routage des requêtes)          │
└────────┬──────────────┬──────────────┬──────────────┬───────────┘
         │              │              │              │
         ▼              ▼              ▼              ▼
    ┌────────┐    ┌────────┐    ┌────────┐    ┌────────┐
    │Service │    │Service │    │Service │    │Service │
    │Utilisat│    │ Produit│    │Commande│    │Paiement│
    │  eurs  │    │   s    │    │   s    │    │        │
    └────┬───┘    └────┬───┘    └────┬───┘    └────┬───┘
         │             │             │             │
         ▼             ▼             ▼             ▼
    ┌────────┐    ┌────────┐    ┌────────┐    ┌────────┐
    │   BD   │    │   BD   │    │   BD   │    │   BD   │
    │Utilisat│    │Produits│    │Commande│    │Paiement│
    └────────┘    └────────┘    └────────┘    └────────┘
```

### Explication détaillée de l'architecture

#### 1. **Client (Interface Utilisateur)**
- Application web, mobile ou desktop
- Envoie des requêtes HTTP vers l'API Gateway
- Affiche les données reçues à l'utilisateur

#### 2. **API Gateway** 
- **Rôle** : Point d'entrée unique pour toutes les requêtes
- **Fonctions** :
  - Routage des requêtes vers le bon microservice
  - Authentification et autorisation
  - Limitation du taux de requêtes (rate limiting)
  - Transformation des requêtes/réponses

#### 3. **Microservices**
Chaque service est **indépendant** et gère une fonctionnalité spécifique :

- **Service Utilisateurs** : Gestion des comptes, authentification
- **Service Produits** : Catalogue, stock, recherche
- **Service Commandes** : Création et suivi des commandes
- **Service Paiement** : Traitement des paiements

**Avantages** :
- ✅ Scalabilité indépendante
- ✅ Déploiement indépendant
- ✅ Technologies différentes possibles
- ✅ Équipes autonomes

#### 4. **Bases de données**
- Chaque microservice a sa propre base de données
- Isolation des données
- Pas de dépendances directes entre services

### Flux de communication - Exemple concret

**Scénario** : Un utilisateur achète un produit

```
1. CLIENT → API Gateway
   GET /api/products/123
   "Je veux voir le produit 123"

2. API Gateway → Service Produits
   Routage de la requête
   
3. Service Produits → BD Produits
   SELECT * FROM products WHERE id=123
   
4. Service Produits → API Gateway → CLIENT
   Response: { "id": 123, "name": "Laptop", "price": 999 }

5. CLIENT → API Gateway
   POST /api/orders
   Body: { "productId": 123, "quantity": 1 }
   
6. API Gateway → Service Commandes
   Création de la commande
   
7. Service Commandes → Service Produits
   Vérification du stock (appel REST interne)
   
8. Service Commandes → Service Paiement
   Traitement du paiement (appel REST interne)
   
9. Retour de la confirmation au CLIENT
```

---

## 📖 Concepts clés {#concepts}

### JSON (JavaScript Object Notation)

Format d'échange de données léger et lisible :

```json
{
  "id": 1,
  "nom": "Jean Dupont",
  "email": "jean@example.com",
  "actif": true,
  "roles": ["admin", "user"],
  "adresse": {
    "rue": "123 Rue de la Paix",
    "ville": "Paris"
  }
}
```

### Endpoint (Point de terminaison)

Une URL qui représente une ressource :
- `https://api.example.com/users` - Collection d'utilisateurs
- `https://api.example.com/users/1` - Utilisateur spécifique
- `https://api.example.com/users/1/orders` - Commandes d'un utilisateur

### Headers HTTP

Métadonnées envoyées avec la requête/réponse :

```
Content-Type: application/json
Authorization: Bearer token123
Accept: application/json
```

---

## 🎓 Exercice 1 : Consommer des données depuis un fichier JSON {#exercice-1}

### Objectif
Créer une application web qui lit des données depuis des fichiers JSON locaux et les affiche dans une interface.

### Étape 1 : Préparation de la structure

Créez la structure suivante :

```
projet-api-rest/
│
├── index.html
├── styles.css
├── app.js
└── data/
    ├── users.json
    └── products.json
```

### Étape 2 : Créer les fichiers JSON

#### `data/users.json`
```json
{
  "users": [
    {
      "id": 1,
      "nom": "Alice Martin",
      "email": "alice@example.com",
      "role": "Administrateur",
      "actif": true
    },
    {
      "id": 2,
      "nom": "Bob Durand",
      "email": "bob@example.com",
      "role": "Utilisateur",
      "actif": true
    },
    {
      "id": 3,
      "nom": "Charlie Petit",
      "email": "charlie@example.com",
      "role": "Utilisateur",
      "actif": false
    }
  ]
}
```

#### `data/products.json`
```json
{
  "products": [
    {
      "id": 1,
      "nom": "Ordinateur Portable",
      "prix": 999.99,
      "categorie": "Électronique",
      "stock": 15,
      "image": "💻"
    },
    {
      "id": 2,
      "nom": "Souris Sans Fil",
      "prix": 29.99,
      "categorie": "Accessoires",
      "stock": 50,
      "image": "🖱️"
    },
    {
      "id": 3,
      "nom": "Clavier Mécanique",
      "prix": 149.99,
      "categorie": "Accessoires",
      "stock": 8,
      "image": "⌨️"
    },
    {
      "id": 4,
      "nom": "Écran 27 pouces",
      "prix": 399.99,
      "categorie": "Électronique",
      "stock": 12,
      "image": "🖥️"
    }
  ]
}
```

### Étape 3 : Créer l'interface HTML

#### `index.html`
```html
<!DOCTYPE html>
<html lang="fr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>TD API REST - Données Locales</title>
    <link rel="stylesheet" href="styles.css">
</head>
<body>
    <div class="container">
        <header>
            <h1>🎯 TD API REST - Exercice 1</h1>
            <p>Consommer des données depuis des fichiers JSON locaux</p>
        </header>

        <div class="buttons">
            <button id="loadUsers" class="btn btn-primary">
                👥 Charger les Utilisateurs
            </button>
            <button id="loadProducts" class="btn btn-success">
                🛍️ Charger les Produits
            </button>
            <button id="clearData" class="btn btn-danger">
                🗑️ Effacer
            </button>
        </div>

        <div id="loading" class="loading hidden">
            <div class="spinner"></div>
            <p>Chargement des données...</p>
        </div>

        <div id="error" class="error hidden"></div>

        <div id="results" class="results"></div>
    </div>

    <script src="app.js"></script>
</body>
</html>
```

### Étape 4 : Créer les styles CSS

#### `styles.css`
```css
* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}

body {
    font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    min-height: 100vh;
    padding: 20px;
}

.container {
    max-width: 1200px;
    margin: 0 auto;
}

header {
    background: white;
    padding: 30px;
    border-radius: 10px;
    text-align: center;
    margin-bottom: 30px;
    box-shadow: 0 10px 30px rgba(0, 0, 0, 0.2);
}

header h1 {
    color: #667eea;
    margin-bottom: 10px;
}

header p {
    color: #666;
}

.buttons {
    display: flex;
    gap: 15px;
    justify-content: center;
    margin-bottom: 30px;
    flex-wrap: wrap;
}

.btn {
    padding: 12px 25px;
    border: none;
    border-radius: 5px;
    font-size: 16px;
    cursor: pointer;
    transition: transform 0.2s, box-shadow 0.2s;
    color: white;
    font-weight: bold;
}

.btn:hover {
    transform: translateY(-2px);
    box-shadow: 0 5px 15px rgba(0, 0, 0, 0.3);
}

.btn-primary {
    background: #667eea;
}

.btn-success {
    background: #48bb78;
}

.btn-danger {
    background: #f56565;
}

.loading {
    background: white;
    padding: 30px;
    border-radius: 10px;
    text-align: center;
    box-shadow: 0 5px 15px rgba(0, 0, 0, 0.2);
}

.spinner {
    width: 50px;
    height: 50px;
    margin: 0 auto 20px;
    border: 5px solid #f3f3f3;
    border-top: 5px solid #667eea;
    border-radius: 50%;
    animation: spin 1s linear infinite;
}

@keyframes spin {
    0% { transform: rotate(0deg); }
    100% { transform: rotate(360deg); }
}

.hidden {
    display: none;
}

.error {
    background: #fed7d7;
    color: #c53030;
    padding: 20px;
    border-radius: 10px;
    margin-bottom: 20px;
    border-left: 5px solid #f56565;
}

.results {
    background: white;
    padding: 30px;
    border-radius: 10px;
    box-shadow: 0 5px 15px rgba(0, 0, 0, 0.2);
}

.results h2 {
    color: #667eea;
    margin-bottom: 20px;
    display: flex;
    align-items: center;
    gap: 10px;
}

.results h2::before {
    content: '📊';
}

.user-card, .product-card {
    background: #f7fafc;
    padding: 20px;
    margin-bottom: 15px;
    border-radius: 8px;
    border-left: 4px solid #667eea;
    transition: transform 0.2s;
}

.user-card:hover, .product-card:hover {
    transform: translateX(5px);
    box-shadow: 0 3px 10px rgba(0, 0, 0, 0.1);
}

.user-header, .product-header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-bottom: 10px;
}

.user-name, .product-name {
    font-size: 20px;
    font-weight: bold;
    color: #2d3748;
}

.badge {
    padding: 5px 10px;
    border-radius: 15px;
    font-size: 12px;
    font-weight: bold;
}

.badge-active {
    background: #c6f6d5;
    color: #22543d;
}

.badge-inactive {
    background: #fed7d7;
    color: #c53030;
}

.user-info, .product-info {
    color: #4a5568;
    line-height: 1.6;
}

.price {
    font-size: 24px;
    font-weight: bold;
    color: #48bb78;
}

.stock {
    display: inline-block;
    padding: 5px 10px;
    background: #bee3f8;
    color: #2c5282;
    border-radius: 5px;
    font-size: 14px;
}

.product-icon {
    font-size: 40px;
    margin-right: 15px;
}

@media (max-width: 768px) {
    .buttons {
        flex-direction: column;
    }
    
    .btn {
        width: 100%;
    }
}
```

### Étape 5 : Créer le code JavaScript

#### `app.js`
```javascript
// ÉTAPE 1 : Récupérer les éléments HTML
const resultsDiv = document.getElementById('results');
const loadingDiv = document.getElementById('loading');
const errorDiv = document.getElementById('error');

// ÉTAPE 2 : Fonction pour charger les utilisateurs
function loadUsers() {
    // Afficher le chargement
    loadingDiv.classList.remove('hidden');
    resultsDiv.innerHTML = '';
    
    // Appeler l'API pour récupérer les données
    fetch('data/users.json')
        .then(response => response.json())  // Convertir en JSON
        .then(data => {
            // Cacher le chargement
            loadingDiv.classList.add('hidden');
            
            // Afficher les utilisateurs
            let html = '<h2>👥 Liste des Utilisateurs</h2>';
            
            data.users.forEach(user => {
                html += `
                    <div class="user-card">
                        <h3>${user.nom}</h3>
                        <p>� ${user.email}</p>
                        <p>🎭 ${user.role}</p>
                        <p>Statut: ${user.actif ? '✅ Actif' : '❌ Inactif'}</p>
                    </div>
                `;
            });
            
            resultsDiv.innerHTML = html;
        })
        .catch(error => {
            // En cas d'erreur
            loadingDiv.classList.add('hidden');
            errorDiv.innerHTML = '❌ Erreur : ' + error.message;
            errorDiv.classList.remove('hidden');
        });
}

// ÉTAPE 3 : Fonction pour charger les produits
function loadProducts() {
    // Afficher le chargement
    loadingDiv.classList.remove('hidden');
    resultsDiv.innerHTML = '';
    
    // Appeler l'API pour récupérer les données
    fetch('data/products.json')
        .then(response => response.json())  // Convertir en JSON
        .then(data => {
            // Cacher le chargement
            loadingDiv.classList.add('hidden');
            
            // Afficher les produits
            let html = '<h2>🛍️ Liste des Produits</h2>';
            
            data.products.forEach(product => {
                html += `
                    <div class="product-card">
                        <h3>${product.image} ${product.nom}</h3>
                        <p><strong>Prix:</strong> ${product.prix} €</p>
                        <p><strong>Catégorie:</strong> ${product.categorie}</p>
                        <p><strong>Stock:</strong> ${product.stock} unités</p>
                    </div>
                `;
            });
            
            resultsDiv.innerHTML = html;
        })
        .catch(error => {
            // En cas d'erreur
            loadingDiv.classList.add('hidden');
            errorDiv.innerHTML = '❌ Erreur : ' + error.message;
            errorDiv.classList.remove('hidden');
        });
}

// ÉTAPE 4 : Fonction pour effacer
function clearData() {
    resultsDiv.innerHTML = '<p>Cliquez sur un bouton pour charger les données</p>';
    errorDiv.classList.add('hidden');
}

// ÉTAPE 5 : Connecter les boutons aux fonctions
document.getElementById('loadUsers').onclick = loadUsers;
document.getElementById('loadProducts').onclick = loadProducts;
document.getElementById('clearData').onclick = clearData;
```

### 📝 Explications du code JavaScript (ligne par ligne)

#### 1. **Fetch API - Appeler l'API**
```javascript
fetch('data/users.json')
```
- `fetch()` = fonction pour récupérer des données
- On lui donne le chemin du fichier JSON

#### 2. **Convertir la réponse en JSON**
```javascript
.then(response => response.json())
```
- `.then()` = "quand la réponse arrive, fais ceci"
- `response.json()` = transforme la réponse en objet JavaScript

#### 3. **Utiliser les données**
```javascript
.then(data => {
    // Ici on a nos données !
    console.log(data);
})
```
- On reçoit les données et on peut les afficher

#### 4. **Gérer les erreurs**
```javascript
.catch(error => {
    // Si quelque chose ne marche pas
    console.log('Erreur:', error);
})
```
- `.catch()` = attrape les erreurs

#### 5. **Boucle forEach pour afficher**
```javascript
data.users.forEach(user => {
    // Pour chaque utilisateur, on crée du HTML
    html += '<div>' + user.nom + '</div>';
});
```
- `forEach()` = parcourt tous les éléments du tableau

### 🚀 Pour tester l'application

**Important** : Les navigateurs modernes bloquent les requêtes `file://` pour des raisons de sécurité.

**Solution** : Utiliser un serveur local

#### Méthode 1 : Avec Python
```bash
# Python 3
python -m http.server 8000

# Puis ouvrir: http://localhost:8000
```

#### Méthode 2 : Avec Node.js
```bash
npx http-server -p 8000
```

#### Méthode 3 : Avec l'extension VS Code
- Installer l'extension "Live Server"
- Clic droit sur `index.html` → "Open with Live Server"

---

## 🌍 Exercice 2 : Consommer une API publique {#exercice-2}

### Objectif
Créer une application qui consomme des données depuis une API publique gratuite sur Internet.

### API utilisée : JSONPlaceholder
**URL** : `https://jsonplaceholder.typicode.com`

Cette API gratuite simule une vraie API REST avec :
- Utilisateurs (`/users`)
- Posts (`/posts`)
- Commentaires (`/comments`)
- Albums (`/albums`)
- Photos (`/photos`)

### Étape 1 : Créer la structure

```
projet-api-publique/
│
├── index.html
├── styles.css
└── app.js
```

### Étape 2 : Créer l'interface HTML

#### `index.html`
```html
<!DOCTYPE html>
<html lang="fr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>TD API REST - API Publique</title>
    <link rel="stylesheet" href="styles.css">
</head>
<body>
    <div class="container">
        <header>
            <h1>🌍 TD API REST - Exercice 2</h1>
            <p>Consommer une API publique sur Internet</p>
        </header>

        <div class="buttons">
            <button id="loadUsers" class="btn btn-primary">👥 Utilisateurs</button>
            <button id="loadPosts" class="btn btn-success">📝 Posts</button>
            <button id="loadPhotos" class="btn btn-info">📸 Photos</button>
        </div>

        <div class="search-bar">
            <input type="text" id="searchInput" placeholder="Rechercher un post...">
            <button id="searchBtn" class="btn btn-primary">🔍 Rechercher</button>
        </div>

        <div id="loading" class="loading hidden">
            <div class="spinner"></div>
            <p>Chargement...</p>
        </div>

        <div id="error" class="error hidden"></div>

        <div id="results" class="results">
            <p>Cliquez sur un bouton pour charger les données</p>
        </div>
    </div>

    <script src="app.js"></script>
</body>
</html>
```

### Étape 3 : Créer les styles CSS

#### `styles.css`
```css
* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}

body {
    font-family: Arial, sans-serif;
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    min-height: 100vh;
    padding: 20px;
}

.container {
    max-width: 1200px;
    margin: 0 auto;
}

header {
    background: white;
    padding: 30px;
    border-radius: 10px;
    text-align: center;
    margin-bottom: 30px;
    box-shadow: 0 5px 15px rgba(0, 0, 0, 0.2);
}

header h1 {
    color: #667eea;
    margin-bottom: 10px;
}

.buttons {
    display: flex;
    gap: 15px;
    justify-content: center;
    margin-bottom: 20px;
}

.search-bar {
    background: white;
    padding: 20px;
    border-radius: 10px;
    margin-bottom: 20px;
    display: flex;
    gap: 10px;
}

.search-bar input {
    flex: 1;
    padding: 10px;
    border: 2px solid #ddd;
    border-radius: 5px;
    font-size: 16px;
}

.btn {
    padding: 12px 25px;
    border: none;
    border-radius: 5px;
    font-size: 16px;
    cursor: pointer;
    color: white;
    font-weight: bold;
}

.btn:hover {
    opacity: 0.9;
}

.btn-primary {
    background: #667eea;
}

.btn-success {
    background: #48bb78;
}

.btn-info {
    background: #0bc5ea;
}

.loading {
    background: white;
    padding: 30px;
    border-radius: 10px;
    text-align: center;
    box-shadow: 0 5px 15px rgba(0, 0, 0, 0.2);
    margin-bottom: 20px;
}

.spinner {
    width: 50px;
    height: 50px;
    margin: 0 auto 20px;
    border: 5px solid #f3f3f3;
    border-top: 5px solid #667eea;
    border-radius: 50%;
    animation: spin 1s linear infinite;
}

@keyframes spin {
    0% { transform: rotate(0deg); }
    100% { transform: rotate(360deg); }
}

.hidden {
    display: none;
}

.error {
    background: #fed7d7;
    color: #c53030;
    padding: 20px;
    border-radius: 10px;
    margin-bottom: 20px;
}

.results {
    background: white;
    padding: 30px;
    border-radius: 10px;
    box-shadow: 0 5px 15px rgba(0, 0, 0, 0.2);
}

.user-card, .post-card, .photo-card {
    background: #f7fafc;
    padding: 20px;
    margin-bottom: 15px;
    border-radius: 8px;
    border-left: 4px solid #667eea;
}

.user-card h3, .post-card h3, .photo-card h3 {
    color: #2d3748;
    margin-bottom: 10px;
}

.photo-card img {
    width: 150px;
    border-radius: 8px;
    margin-bottom: 10px;
}
```

### Étape 4 : Créer le code JavaScript

#### `app.js`
```javascript
// URL de l'API
const API_URL = 'https://jsonplaceholder.typicode.com';

// Récupérer les éléments HTML
const resultsDiv = document.getElementById('results');
const loadingDiv = document.getElementById('loading');
const errorDiv = document.getElementById('error');

// ============================================
// FONCTION POUR CHARGER LES UTILISATEURS
// ============================================
function loadUsers() {
    // Afficher le chargement
    loadingDiv.classList.remove('hidden');
    resultsDiv.innerHTML = '';
    
    // Appeler l'API
    fetch(API_URL + '/users')
        .then(response => response.json())
        .then(users => {
            // Cacher le chargement
            loadingDiv.classList.add('hidden');
            
            // Créer le HTML pour chaque utilisateur
            let html = '<h2>👥 Utilisateurs (' + users.length + ')</h2>';
            
            users.forEach(user => {
                html += `
                    <div class="user-card">
                        <h3>${user.name}</h3>
                        <p>📧 ${user.email}</p>
                        <p>📱 ${user.phone}</p>
                        <p>🏢 ${user.company.name}</p>
                        <p>🌍 ${user.address.city}</p>
                    </div>
                `;
            });
            
            resultsDiv.innerHTML = html;
        })
        .catch(error => {
            loadingDiv.classList.add('hidden');
            errorDiv.innerHTML = '❌ Erreur : ' + error.message;
            errorDiv.classList.remove('hidden');
        });
}

// ============================================
// FONCTION POUR CHARGER LES POSTS
// ============================================
function loadPosts() {
    // Afficher le chargement
    loadingDiv.classList.remove('hidden');
    resultsDiv.innerHTML = '';
    
    // Appeler l'API (limité à 10 posts)
    fetch(API_URL + '/posts?_limit=10')
        .then(response => response.json())
        .then(posts => {
            // Cacher le chargement
            loadingDiv.classList.add('hidden');
            
            // Créer le HTML pour chaque post
            let html = '<h2>📝 Posts (' + posts.length + ')</h2>';
            
            posts.forEach(post => {
                html += `
                    <div class="post-card">
                        <h3>${post.title}</h3>
                        <p>${post.body}</p>
                        <small>Par utilisateur #${post.userId}</small>
                    </div>
                `;
            });
            
            resultsDiv.innerHTML = html;
        })
        .catch(error => {
            loadingDiv.classList.add('hidden');
            errorDiv.innerHTML = '❌ Erreur : ' + error.message;
            errorDiv.classList.remove('hidden');
        });
}

// ============================================
// FONCTION POUR CHARGER LES PHOTOS
// ============================================
function loadPhotos() {
    // Afficher le chargement
    loadingDiv.classList.remove('hidden');
    resultsDiv.innerHTML = '';
    
    // Appeler l'API (limité à 6 photos)
    fetch(API_URL + '/photos?_limit=6')
        .then(response => response.json())
        .then(photos => {
            // Cacher le chargement
            loadingDiv.classList.add('hidden');
            
            // Créer le HTML pour chaque photo
            let html = '<h2>📸 Photos (' + photos.length + ')</h2>';
            
            photos.forEach(photo => {
                html += `
                    <div class="photo-card">
                        <img src="${photo.thumbnailUrl}" alt="${photo.title}">
                        <h3>${photo.title}</h3>
                    </div>
                `;
            });
            
            resultsDiv.innerHTML = html;
        })
        .catch(error => {
            loadingDiv.classList.add('hidden');
            errorDiv.innerHTML = '❌ Erreur : ' + error.message;
            errorDiv.classList.remove('hidden');
        });
}

// ============================================
// FONCTION POUR RECHERCHER
// ============================================
function searchPosts() {
    const searchInput = document.getElementById('searchInput');
    const searchTerm = searchInput.value;
    
    if (searchTerm === '') {
        alert('Veuillez entrer un terme de recherche');
        return;
    }
    
    // Afficher le chargement
    loadingDiv.classList.remove('hidden');
    resultsDiv.innerHTML = '';
    
    // Appeler l'API pour tous les posts
    fetch(API_URL + '/posts')
        .then(response => response.json())
        .then(posts => {
            // Filtrer les posts qui contiennent le terme recherché
            const filteredPosts = posts.filter(post => 
                post.title.includes(searchTerm) || 
                post.body.includes(searchTerm)
            );
            
            // Cacher le chargement
            loadingDiv.classList.add('hidden');
            
            if (filteredPosts.length === 0) {
                resultsDiv.innerHTML = '<p>Aucun résultat trouvé</p>';
                return;
            }
            
            // Créer le HTML
            let html = '<h2>🔍 Résultats (' + filteredPosts.length + ')</h2>';
            
            filteredPosts.forEach(post => {
                html += `
                    <div class="post-card">
                        <h3>${post.title}</h3>
                        <p>${post.body}</p>
                    </div>
                `;
            });
            
            resultsDiv.innerHTML = html;
        })
        .catch(error => {
            loadingDiv.classList.add('hidden');
            errorDiv.innerHTML = '❌ Erreur : ' + error.message;
            errorDiv.classList.remove('hidden');
        });
}

// ============================================
// CONNECTER LES BOUTONS
// ============================================
document.getElementById('loadUsers').onclick = loadUsers;
document.getElementById('loadPosts').onclick = loadPosts;
document.getElementById('loadPhotos').onclick = loadPhotos;
document.getElementById('searchBtn').onclick = searchPosts;
```

### 📝 Explications détaillées du code

#### 1. **Structure de base**
```javascript
const API_URL = 'https://jsonplaceholder.typicode.com';
```
- On définit l'adresse de l'API

#### 2. **Appeler l'API avec fetch()**
```javascript
fetch(API_URL + '/users')
```
- `fetch()` = demande des données à l'API
- On donne l'URL complète

#### 3. **Convertir en JSON**
```javascript
.then(response => response.json())
```
- `.then()` = "quand les données arrivent..."
- `response.json()` = transforme en objet JavaScript

#### 4. **Utiliser les données**
```javascript
.then(users => {
    // Ici on a les utilisateurs !
})
```
- On reçoit les données et on peut les afficher

#### 5. **Boucle pour afficher**
```javascript
users.forEach(user => {
    html += '<div>' + user.name + '</div>';
});
```
- `forEach()` = pour chaque élément du tableau
- On crée du HTML pour chaque utilisateur

#### 6. **Gérer les erreurs**
```javascript
.catch(error => {
    // Si ça ne marche pas
})
```
- `.catch()` = attrape les erreurs

### 🎯 Points d'apprentissage clés

1. **Fetch API** : Fonction simple pour appeler une API
2. **Promises** : `.then()` pour gérer les réponses
3. **JSON** : Format d'échange de données
4. **forEach** : Boucle pour parcourir les données
5. **Gestion d'erreurs** : `.catch()` pour attraper les problèmes

### 🚀 Pour tester l'application

Ouvrez simplement `index.html` dans votre navigateur - pas besoin de serveur local car l'API est sur Internet !

### 📚 Autres API publiques gratuites à explorer

1. **REST Countries** : `https://restcountries.com/v3.1/all`
   - Informations sur tous les pays

2. **Open Meteo** : `https://api.open-meteo.com/v1/forecast?latitude=48.85&longitude=2.35&current_weather=true`
   - Météo en temps réel

3. **Dog API** : `https://dog.ceo/api/breeds/image/random`
   - Images aléatoires de chiens

4. **Advice Slip** : `https://api.adviceslip.com/advice`
   - Conseils aléatoires

---

## 📊 Comparaison des deux exercices

| Aspect | Exercice 1 (Local) | Exercice 2 (API Publique) |
|--------|-------------------|---------------------------|
| **Source** | Fichiers JSON locaux | API REST sur Internet |
| **Serveur** | Nécessaire | Pas nécessaire |
| **Données** | Statiques | Dynamiques |
| **Latence** | Très faible | Variable (réseau) |
| **Disponibilité** | 100% | Dépend de l'API |
| **Cas d'usage** | Prototypage, tests | Production |

---

## ✅ Checklist d'apprentissage

Après ce TD, vous devriez être capable de :

- [ ] Expliquer ce qu'est une API REST
- [ ] Comprendre les méthodes HTTP (GET, POST, etc.)
- [ ] Utiliser l'API Fetch en JavaScript
- [ ] Gérer les promesses avec async/await
- [ ] Traiter les erreurs avec try/catch
- [ ] Comprendre l'architecture microservices
- [ ] Lire et manipuler des données JSON
- [ ] Afficher des données dynamiques dans une interface
- [ ] Consommer des API publiques
- [ ] Utiliser des paramètres d'URL

---

## 🎓 Exercices supplémentaires

### Exercice 3 : Améliorer l'application
1. Ajouter un bouton "Rafraîchir" pour recharger les données
2. Implémenter une pagination pour les résultats
3. Ajouter des filtres (tri par nom, date, etc.)
4. Sauvegarder les résultats dans le localStorage

### Exercice 4 : Créer votre propre API
1. Utiliser Node.js et Express pour créer une API simple
2. Implémenter les méthodes CRUD (Create, Read, Update, Delete)
3. Consommer votre API depuis une interface web

### Exercice 5 : Intégration avancée
1. Combiner plusieurs API (utilisateurs + posts + commentaires)
2. Afficher les relations entre les données
3. Implémenter une vraie recherche avec auto-complétion

---

## 📖 Ressources complémentaires

### Documentation
- [MDN - Fetch API](https://developer.mozilla.org/fr/docs/Web/API/Fetch_API)
- [MDN - Promises](https://developer.mozilla.org/fr/docs/Web/JavaScript/Reference/Global_Objects/Promise)
- [REST API Tutorial](https://restfulapi.net/)

### Outils utiles
- **Postman** : Tester des API REST
- **JSON Formatter** : Extension Chrome pour visualiser le JSON
- **DevTools** : Onglet Network pour voir les requêtes HTTP

### API publiques gratuites
- [Public APIs](https://github.com/public-apis/public-apis) - Liste de centaines d'API
- [JSONPlaceholder](https://jsonplaceholder.typicode.com/) - API de test
- [RapidAPI](https://rapidapi.com/) - Marketplace d'API

---

## 🎉 Conclusion

Félicitations ! Vous avez maintenant une compréhension solide de :

1. **API REST** : Comment elles fonctionnent et pourquoi elles sont importantes
2. **Microservices** : Architecture moderne des applications
3. **Consommation d'API** : Deux approches (locale et distante)
4. **JavaScript moderne** : Fetch, async/await, Promises
5. **Développement web** : Interface interactive et responsive

Les API REST sont au cœur du développement web moderne. Cette connaissance vous servira dans tous vos futurs projets !

---

**Bon apprentissage ! 🚀**
