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
// Sélection des éléments DOM
const loadUsersBtn = document.getElementById('loadUsers');
const loadProductsBtn = document.getElementById('loadProducts');
const clearDataBtn = document.getElementById('clearData');
const resultsDiv = document.getElementById('results');
const loadingDiv = document.getElementById('loading');
const errorDiv = document.getElementById('error');

// Fonction pour afficher le chargement
function showLoading() {
    loadingDiv.classList.remove('hidden');
    resultsDiv.innerHTML = '';
    errorDiv.classList.add('hidden');
}

// Fonction pour cacher le chargement
function hideLoading() {
    loadingDiv.classList.add('hidden');
}

// Fonction pour afficher une erreur
function showError(message) {
    errorDiv.innerHTML = `
        <strong>❌ Erreur :</strong> ${message}
    `;
    errorDiv.classList.remove('hidden');
}

// Fonction pour charger les utilisateurs
async function loadUsers() {
    showLoading();
    
    try {
        // Simulation d'un délai réseau
        await new Promise(resolve => setTimeout(resolve, 500));
        
        // Fetch API - Récupération des données
        const response = await fetch('data/users.json');
        
        // Vérification du statut de la réponse
        if (!response.ok) {
            throw new Error(`Erreur HTTP: ${response.status}`);
        }
        
        // Conversion de la réponse en JSON
        const data = await response.json();
        
        // Affichage des utilisateurs
        displayUsers(data.users);
        
    } catch (error) {
        showError(`Impossible de charger les utilisateurs: ${error.message}`);
        console.error('Erreur:', error);
    } finally {
        hideLoading();
    }
}

// Fonction pour afficher les utilisateurs
function displayUsers(users) {
    resultsDiv.innerHTML = `
        <h2>Utilisateurs chargés (${users.length})</h2>
        ${users.map(user => `
            <div class="user-card">
                <div class="user-header">
                    <span class="user-name">👤 ${user.nom}</span>
                    <span class="badge ${user.actif ? 'badge-active' : 'badge-inactive'}">
                        ${user.actif ? '✓ Actif' : '✗ Inactif'}
                    </span>
                </div>
                <div class="user-info">
                    <p><strong>📧 Email:</strong> ${user.email}</p>
                    <p><strong>🎭 Rôle:</strong> ${user.role}</p>
                    <p><strong>🆔 ID:</strong> ${user.id}</p>
                </div>
            </div>
        `).join('')}
    `;
}

// Fonction pour charger les produits
async function loadProducts() {
    showLoading();
    
    try {
        // Simulation d'un délai réseau
        await new Promise(resolve => setTimeout(resolve, 500));
        
        // Fetch API - Récupération des données
        const response = await fetch('data/products.json');
        
        // Vérification du statut de la réponse
        if (!response.ok) {
            throw new Error(`Erreur HTTP: ${response.status}`);
        }
        
        // Conversion de la réponse en JSON
        const data = await response.json();
        
        // Affichage des produits
        displayProducts(data.products);
        
    } catch (error) {
        showError(`Impossible de charger les produits: ${error.message}`);
        console.error('Erreur:', error);
    } finally {
        hideLoading();
    }
}

// Fonction pour afficher les produits
function displayProducts(products) {
    resultsDiv.innerHTML = `
        <h2>Produits disponibles (${products.length})</h2>
        ${products.map(product => `
            <div class="product-card">
                <div class="product-header">
                    <div style="display: flex; align-items: center;">
                        <span class="product-icon">${product.image}</span>
                        <div>
                            <div class="product-name">${product.nom}</div>
                            <small style="color: #718096;">Catégorie: ${product.categorie}</small>
                        </div>
                    </div>
                    <span class="price">${product.prix.toFixed(2)} €</span>
                </div>
                <div class="product-info">
                    <p><strong>🆔 ID:</strong> ${product.id}</p>
                    <p><strong>📦 Stock:</strong> <span class="stock">${product.stock} unités</span></p>
                </div>
            </div>
        `).join('')}
    `;
}

// Fonction pour effacer les données
function clearData() {
    resultsDiv.innerHTML = '';
    errorDiv.classList.add('hidden');
}

// Ajout des événements sur les boutons
loadUsersBtn.addEventListener('click', loadUsers);
loadProductsBtn.addEventListener('click', loadProducts);
clearDataBtn.addEventListener('click', clearData);

// Message de bienvenue
resultsDiv.innerHTML = `
    <div style="text-align: center; padding: 40px; color: #718096;">
        <h2 style="color: #667eea;">👋 Bienvenue dans le TD API REST</h2>
        <p>Cliquez sur les boutons ci-dessus pour charger les données</p>
    </div>
`;
```

### 📝 Explications du code JavaScript

#### 1. **Fetch API**
```javascript
const response = await fetch('data/users.json');
```
- `fetch()` est une fonction moderne pour faire des requêtes HTTP
- Retourne une **Promise** (promesse)
- `await` attend que la promesse soit résolue

#### 2. **Async/Await**
```javascript
async function loadUsers() {
    // Code asynchrone
}
```
- `async` indique une fonction asynchrone
- `await` met en pause l'exécution jusqu'à la résolution de la promesse

#### 3. **Try/Catch**
```javascript
try {
    // Code qui peut générer une erreur
} catch (error) {
    // Gestion de l'erreur
}
```
- Permet de capturer et gérer les erreurs

#### 4. **Traitement de la réponse**
```javascript
if (!response.ok) {
    throw new Error(`Erreur HTTP: ${response.status}`);
}
const data = await response.json();
```
- Vérification du statut de la réponse
- Conversion en JSON

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
            <small>API: JSONPlaceholder</small>
        </header>

        <div class="tabs">
            <button class="tab-btn active" data-tab="users">
                👥 Utilisateurs
            </button>
            <button class="tab-btn" data-tab="posts">
                📝 Posts
            </button>
            <button class="tab-btn" data-tab="photos">
                📸 Photos
            </button>
            <button class="tab-btn" data-tab="search">
                🔍 Recherche
            </button>
        </div>

        <div id="users-tab" class="tab-content active">
            <div class="action-bar">
                <button id="loadAllUsers" class="btn btn-primary">
                    Charger tous les utilisateurs
                </button>
                <button id="loadRandomUser" class="btn btn-secondary">
                    Utilisateur aléatoire
                </button>
            </div>
        </div>

        <div id="posts-tab" class="tab-content">
            <div class="action-bar">
                <button id="loadAllPosts" class="btn btn-primary">
                    Charger tous les posts
                </button>
                <input type="number" id="userIdInput" placeholder="ID utilisateur" min="1" max="10">
                <button id="loadUserPosts" class="btn btn-secondary">
                    Posts d'un utilisateur
                </button>
            </div>
        </div>

        <div id="photos-tab" class="tab-content">
            <div class="action-bar">
                <button id="loadPhotos" class="btn btn-primary">
                    Charger des photos
                </button>
                <input type="number" id="photoLimit" placeholder="Nombre (1-20)" min="1" max="20" value="6">
            </div>
        </div>

        <div id="search-tab" class="tab-content">
            <div class="search-bar">
                <input type="text" id="searchInput" placeholder="Rechercher un post...">
                <button id="searchBtn" class="btn btn-primary">🔍 Rechercher</button>
            </div>
        </div>

        <div id="loading" class="loading hidden">
            <div class="spinner"></div>
            <p>Chargement des données depuis l'API...</p>
        </div>

        <div id="error" class="error hidden"></div>

        <div id="stats" class="stats hidden">
            <div class="stat-item">
                <span class="stat-label">⏱️ Temps de réponse:</span>
                <span id="responseTime" class="stat-value">-</span>
            </div>
            <div class="stat-item">
                <span class="stat-label">📊 Éléments chargés:</span>
                <span id="itemsCount" class="stat-value">-</span>
            </div>
            <div class="stat-item">
                <span class="stat-label">🌐 Statut HTTP:</span>
                <span id="httpStatus" class="stat-value">-</span>
            </div>
        </div>

        <div id="results" class="results"></div>
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
    font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    min-height: 100vh;
    padding: 20px;
}

.container {
    max-width: 1400px;
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
    margin-bottom: 5px;
}

header small {
    color: #999;
    font-style: italic;
}

.tabs {
    display: flex;
    gap: 10px;
    margin-bottom: 20px;
    flex-wrap: wrap;
}

.tab-btn {
    flex: 1;
    padding: 15px;
    border: none;
    background: white;
    border-radius: 8px;
    font-size: 16px;
    font-weight: bold;
    cursor: pointer;
    transition: all 0.3s;
    box-shadow: 0 3px 10px rgba(0, 0, 0, 0.1);
}

.tab-btn:hover {
    transform: translateY(-2px);
    box-shadow: 0 5px 15px rgba(0, 0, 0, 0.2);
}

.tab-btn.active {
    background: #667eea;
    color: white;
}

.tab-content {
    display: none;
}

.tab-content.active {
    display: block;
}

.action-bar {
    background: white;
    padding: 20px;
    border-radius: 10px;
    margin-bottom: 20px;
    display: flex;
    gap: 10px;
    flex-wrap: wrap;
    align-items: center;
    box-shadow: 0 3px 10px rgba(0, 0, 0, 0.1);
}

.search-bar {
    background: white;
    padding: 20px;
    border-radius: 10px;
    margin-bottom: 20px;
    display: flex;
    gap: 10px;
    box-shadow: 0 3px 10px rgba(0, 0, 0, 0.1);
}

.search-bar input {
    flex: 1;
    padding: 12px;
    border: 2px solid #e2e8f0;
    border-radius: 5px;
    font-size: 16px;
}

input[type="number"] {
    padding: 12px;
    border: 2px solid #e2e8f0;
    border-radius: 5px;
    font-size: 16px;
    width: 150px;
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

.btn-secondary {
    background: #48bb78;
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
    border-left: 5px solid #f56565;
}

.stats {
    background: white;
    padding: 20px;
    border-radius: 10px;
    margin-bottom: 20px;
    display: flex;
    justify-content: space-around;
    flex-wrap: wrap;
    gap: 20px;
    box-shadow: 0 3px 10px rgba(0, 0, 0, 0.1);
}

.stat-item {
    text-align: center;
}

.stat-label {
    display: block;
    color: #718096;
    font-size: 14px;
    margin-bottom: 5px;
}

.stat-value {
    display: block;
    color: #667eea;
    font-size: 24px;
    font-weight: bold;
}

.results {
    display: grid;
    grid-template-columns: repeat(auto-fill, minmax(300px, 1fr));
    gap: 20px;
}

.user-card, .post-card, .photo-card {
    background: white;
    padding: 20px;
    border-radius: 10px;
    box-shadow: 0 3px 10px rgba(0, 0, 0, 0.1);
    transition: transform 0.2s;
}

.user-card:hover, .post-card:hover, .photo-card:hover {
    transform: translateY(-5px);
    box-shadow: 0 5px 20px rgba(0, 0, 0, 0.2);
}

.card-header {
    border-bottom: 2px solid #e2e8f0;
    padding-bottom: 10px;
    margin-bottom: 15px;
}

.card-title {
    font-size: 18px;
    font-weight: bold;
    color: #2d3748;
    margin-bottom: 5px;
}

.card-subtitle {
    font-size: 14px;
    color: #718096;
}

.card-body {
    color: #4a5568;
    line-height: 1.6;
}

.card-body p {
    margin-bottom: 10px;
}

.card-body strong {
    color: #2d3748;
}

.photo-card img {
    width: 100%;
    border-radius: 8px;
    margin-bottom: 10px;
}

.badge {
    display: inline-block;
    padding: 5px 10px;
    border-radius: 15px;
    font-size: 12px;
    font-weight: bold;
    background: #bee3f8;
    color: #2c5282;
}

@media (max-width: 768px) {
    .tabs, .action-bar, .search-bar {
        flex-direction: column;
    }
    
    .tab-btn, .btn {
        width: 100%;
    }
    
    .results {
        grid-template-columns: 1fr;
    }
}
```

### Étape 4 : Créer le code JavaScript

#### `app.js`
```javascript
// URL de base de l'API
const API_BASE_URL = 'https://jsonplaceholder.typicode.com';

// Sélection des éléments DOM
const resultsDiv = document.getElementById('results');
const loadingDiv = document.getElementById('loading');
const errorDiv = document.getElementById('error');
const statsDiv = document.getElementById('stats');
const responseTimeSpan = document.getElementById('responseTime');
const itemsCountSpan = document.getElementById('itemsCount');
const httpStatusSpan = document.getElementById('httpStatus');

// Gestion des onglets
const tabButtons = document.querySelectorAll('.tab-btn');
const tabContents = document.querySelectorAll('.tab-content');

tabButtons.forEach(button => {
    button.addEventListener('click', () => {
        const tabName = button.dataset.tab;
        
        // Désactiver tous les onglets
        tabButtons.forEach(btn => btn.classList.remove('active'));
        tabContents.forEach(content => content.classList.remove('active'));
        
        // Activer l'onglet sélectionné
        button.classList.add('active');
        document.getElementById(`${tabName}-tab`).classList.add('active');
        
        // Effacer les résultats
        clearResults();
    });
});

// Fonctions utilitaires
function showLoading() {
    loadingDiv.classList.remove('hidden');
    resultsDiv.innerHTML = '';
    errorDiv.classList.add('hidden');
    statsDiv.classList.add('hidden');
}

function hideLoading() {
    loadingDiv.classList.add('hidden');
}

function showError(message) {
    errorDiv.innerHTML = `<strong>❌ Erreur :</strong> ${message}`;
    errorDiv.classList.remove('hidden');
}

function showStats(responseTime, itemsCount, httpStatus) {
    responseTimeSpan.textContent = `${responseTime} ms`;
    itemsCountSpan.textContent = itemsCount;
    httpStatusSpan.textContent = httpStatus;
    statsDiv.classList.remove('hidden');
}

function clearResults() {
    resultsDiv.innerHTML = '';
    errorDiv.classList.add('hidden');
    statsDiv.classList.add('hidden');
}

// Fonction générique pour faire des requêtes API
async function fetchAPI(endpoint) {
    const startTime = performance.now();
    
    try {
        const response = await fetch(`${API_BASE_URL}${endpoint}`);
        const endTime = performance.now();
        const responseTime = Math.round(endTime - startTime);
        
        if (!response.ok) {
            throw new Error(`Erreur HTTP: ${response.status} ${response.statusText}`);
        }
        
        const data = await response.json();
        
        return {
            data,
            responseTime,
            status: response.status
        };
        
    } catch (error) {
        throw new Error(`Impossible de récupérer les données: ${error.message}`);
    }
}

// ============================================
// ONGLET UTILISATEURS
// ============================================

document.getElementById('loadAllUsers').addEventListener('click', loadAllUsers);
document.getElementById('loadRandomUser').addEventListener('click', loadRandomUser);

async function loadAllUsers() {
    showLoading();
    
    try {
        const result = await fetchAPI('/users');
        displayUsers(result.data);
        showStats(result.responseTime, result.data.length, result.status);
    } catch (error) {
        showError(error.message);
    } finally {
        hideLoading();
    }
}

async function loadRandomUser() {
    showLoading();
    
    try {
        const randomId = Math.floor(Math.random() * 10) + 1;
        const result = await fetchAPI(`/users/${randomId}`);
        displayUsers([result.data]);
        showStats(result.responseTime, 1, result.status);
    } catch (error) {
        showError(error.message);
    } finally {
        hideLoading();
    }
}

function displayUsers(users) {
    resultsDiv.innerHTML = users.map(user => `
        <div class="user-card">
            <div class="card-header">
                <div class="card-title">👤 ${user.name}</div>
                <div class="card-subtitle">@${user.username}</div>
            </div>
            <div class="card-body">
                <p><strong>📧 Email:</strong> ${user.email}</p>
                <p><strong>📱 Téléphone:</strong> ${user.phone}</p>
                <p><strong>🌐 Site web:</strong> ${user.website}</p>
                <p><strong>🏢 Entreprise:</strong> ${user.company.name}</p>
                <p><strong>📍 Ville:</strong> ${user.address.city}</p>
                <p><span class="badge">ID: ${user.id}</span></p>
            </div>
        </div>
    `).join('');
}

// ============================================
// ONGLET POSTS
// ============================================

document.getElementById('loadAllPosts').addEventListener('click', loadAllPosts);
document.getElementById('loadUserPosts').addEventListener('click', loadUserPosts);

async function loadAllPosts() {
    showLoading();
    
    try {
        const result = await fetchAPI('/posts?_limit=12');
        displayPosts(result.data);
        showStats(result.responseTime, result.data.length, result.status);
    } catch (error) {
        showError(error.message);
    } finally {
        hideLoading();
    }
}

async function loadUserPosts() {
    const userId = document.getElementById('userIdInput').value;
    
    if (!userId || userId < 1 || userId > 10) {
        showError('Veuillez entrer un ID utilisateur valide (1-10)');
        return;
    }
    
    showLoading();
    
    try {
        const result = await fetchAPI(`/posts?userId=${userId}`);
        
        if (result.data.length === 0) {
            showError('Aucun post trouvé pour cet utilisateur');
            return;
        }
        
        displayPosts(result.data);
        showStats(result.responseTime, result.data.length, result.status);
    } catch (error) {
        showError(error.message);
    } finally {
        hideLoading();
    }
}

function displayPosts(posts) {
    resultsDiv.innerHTML = posts.map(post => `
        <div class="post-card">
            <div class="card-header">
                <div class="card-title">📝 ${post.title}</div>
                <div class="card-subtitle">Par utilisateur #${post.userId}</div>
            </div>
            <div class="card-body">
                <p>${post.body}</p>
                <p><span class="badge">Post ID: ${post.id}</span></p>
            </div>
        </div>
    `).join('');
}

// ============================================
// ONGLET PHOTOS
// ============================================

document.getElementById('loadPhotos').addEventListener('click', loadPhotos);

async function loadPhotos() {
    const limit = document.getElementById('photoLimit').value || 6;
    
    if (limit < 1 || limit > 20) {
        showError('Veuillez entrer un nombre entre 1 et 20');
        return;
    }
    
    showLoading();
    
    try {
        const result = await fetchAPI(`/photos?_limit=${limit}`);
        displayPhotos(result.data);
        showStats(result.responseTime, result.data.length, result.status);
    } catch (error) {
        showError(error.message);
    } finally {
        hideLoading();
    }
}

function displayPhotos(photos) {
    resultsDiv.innerHTML = photos.map(photo => `
        <div class="photo-card">
            <img src="${photo.thumbnailUrl}" alt="${photo.title}">
            <div class="card-header">
                <div class="card-title">${photo.title}</div>
            </div>
            <div class="card-body">
                <p><span class="badge">Album: ${photo.albumId}</span></p>
                <p><span class="badge">Photo ID: ${photo.id}</span></p>
            </div>
        </div>
    `).join('');
}

// ============================================
// ONGLET RECHERCHE
// ============================================

document.getElementById('searchBtn').addEventListener('click', searchPosts);
document.getElementById('searchInput').addEventListener('keypress', (e) => {
    if (e.key === 'Enter') searchPosts();
});

async function searchPosts() {
    const searchTerm = document.getElementById('searchInput').value.trim();
    
    if (!searchTerm) {
        showError('Veuillez entrer un terme de recherche');
        return;
    }
    
    showLoading();
    
    try {
        // Charger tous les posts
        const result = await fetchAPI('/posts');
        
        // Filtrer les posts qui contiennent le terme de recherche
        const filteredPosts = result.data.filter(post => 
            post.title.toLowerCase().includes(searchTerm.toLowerCase()) ||
            post.body.toLowerCase().includes(searchTerm.toLowerCase())
        );
        
        if (filteredPosts.length === 0) {
            showError(`Aucun résultat trouvé pour "${searchTerm}"`);
            hideLoading();
            return;
        }
        
        displayPosts(filteredPosts);
        showStats(result.responseTime, filteredPosts.length, result.status);
    } catch (error) {
        showError(error.message);
    } finally {
        hideLoading();
    }
}

// Message de bienvenue initial
resultsDiv.innerHTML = `
    <div style="grid-column: 1 / -1; text-align: center; padding: 60px 20px; color: white;">
        <h2 style="font-size: 32px; margin-bottom: 20px;">🚀 Prêt à explorer les API REST ?</h2>
        <p style="font-size: 18px; margin-bottom: 15px;">
            Sélectionnez un onglet ci-dessus et cliquez sur un bouton pour charger les données
        </p>
        <p style="font-size: 14px; opacity: 0.8;">
            Les données proviennent de l'API publique JSONPlaceholder
        </p>
    </div>
`;
```

### 📝 Explications détaillées du code

#### 1. **Structure de l'API**
```javascript
const API_BASE_URL = 'https://jsonplaceholder.typicode.com';
```
- URL de base de l'API
- Tous les endpoints sont relatifs à cette URL

#### 2. **Fonction générique fetchAPI**
```javascript
async function fetchAPI(endpoint) {
    const startTime = performance.now();
    const response = await fetch(`${API_BASE_URL}${endpoint}`);
    const endTime = performance.now();
    // ...
}
```
- Réutilisable pour tous les appels API
- Mesure le temps de réponse
- Gère les erreurs de manière centralisée

#### 3. **Paramètres d'URL (Query Parameters)**
```javascript
'/posts?userId=1'           // Filtrer par userId
'/posts?_limit=12'          // Limiter à 12 résultats
'/photos?_limit=6'          // Limiter à 6 photos
```

#### 4. **Gestion des onglets**
- Interface multi-onglets pour différentes fonctionnalités
- Chaque onglet a ses propres contrôles

#### 5. **Statistiques de performance**
- Temps de réponse API
- Nombre d'éléments chargés
- Code de statut HTTP

### 🎯 Points d'apprentissage clés

1. **Requêtes HTTP réelles** : Contrairement à l'exercice 1, les données viennent d'Internet
2. **Paramètres d'URL** : Filtrage et limitation des résultats
3. **Gestion d'état** : Affichage de loading, erreurs, succès
4. **Performance** : Mesure du temps de réponse
5. **Interface riche** : Onglets, recherche, filtres

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
