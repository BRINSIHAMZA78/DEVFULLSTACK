# GUIDE SIMPLIFIE - API REST POUR DEBUTANTS

## TABLE DES MATIERES

1. [PARTIE 1 : COURS THEORIQUE](#partie1)
   - Definitions essentielles
   - JSON vs XML
   - Framework Express
   - Architecture des applications

2. [PARTIE 2 : TRAVAUX DIRIGES (TD)](#partie2)
   - Schema de l'architecture
   - Etapes de creation
   - Principe de fonctionnement
   - Affichage des donnees

---

# PARTIE 1 : COURS THEORIQUE {#partie1}

## 1. DEFINITIONS ESSENTIELLES

### Qu'est-ce qu'une API ?

**Definition simple :**
Une API (Application Programming Interface) est un intermediaire qui permet a deux applications de communiquer.

**Analogie du restaurant :**
```
Vous (Client) -----> Serveur (API) -----> Cuisine (Base de donnees)
                        |
                     Reponse
```

- **Vous** = L'application qui fait une demande
- **Serveur** = L'API qui transmet votre demande
- **Cuisine** = Le systeme qui prepare les donnees
- **Plat** = La reponse que vous recevez

### Qu'est-ce que REST ?

**REST** = Representational State Transfer

**Definition simple :**
REST est une facon d'organiser une API en utilisant les URL et les methodes HTTP standard.

**Les 4 operations principales (CRUD) :**

| Operation | Methode HTTP | Exemple d'URL | Action |
|-----------|--------------|---------------|--------|
| **C**reate | POST | `/api/equipes` | Creer une nouvelle equipe |
| **R**ead | GET | `/api/equipes` | Lire toutes les equipes |
| **U**pdate | PUT | `/api/equipes/1` | Modifier l'equipe 1 |
| **D**elete | DELETE | `/api/equipes/1` | Supprimer l'equipe 1 |

### Les codes de statut HTTP

**Codes de succes (2xx) :**
- `200 OK` : Tout s'est bien passe
- `201 Created` : Une nouvelle ressource a ete creee

**Codes d'erreur client (4xx) :**
- `400 Bad Request` : Les donnees envoyees sont incorrectes
- `404 Not Found` : La ressource demandee n'existe pas
- `409 Conflict` : La ressource existe deja

**Codes d'erreur serveur (5xx) :**
- `500 Internal Server Error` : Probleme sur le serveur

---

## 2. JSON vs XML

### Qu'est-ce que JSON ?

**JSON** = JavaScript Object Notation

C'est un format de texte pour echanger des donnees entre applications.

**Exemple de donnees d'une equipe en JSON :**
```json
{
  "id": 1,
  "name": "Real Madrid",
  "country": "Spain",
  "joueurs": ["Benzema", "Modric"]
}
```

**Caracteristiques :**
- Facile a lire pour les humains
- Leger (petite taille)
- Rapide a traiter
- Supporte par tous les langages

### Qu'est-ce que XML ?

**XML** = eXtensible Markup Language

C'est l'ancien format utilise avant JSON.

**La meme equipe en XML :**
```xml
<equipe>
  <id>1</id>
  <name>Real Madrid</name>
  <country>Spain</country>
  <joueurs>
    <joueur>Benzema</joueur>
    <joueur>Modric</joueur>
  </joueurs>
</equipe>
```

### COMPARAISON : Pourquoi JSON est meilleur ?

| Critere | JSON | XML |
|---------|------|-----|
| **Lisibilite** | Tres facile | Complique |
| **Taille** | Petit (leger) | Grand (lourd) |
| **Vitesse** | Rapide | Lent |
| **Popularite** | Standard moderne | Ancien |

**Exemple concret :**

Donnees d'une personne :

**JSON (52 caracteres) :**
```json
{"nom":"Dupont","age":25,"ville":"Paris"}
```

**XML (89 caracteres) :**
```xml
<personne>
  <nom>Dupont</nom>
  <age>25</age>
  <ville>Paris</ville>
</personne>
```

**Conclusion :** JSON est 40% plus leger que XML !

---

## 3. FRAMEWORK EXPRESS

### Qu'est-ce qu'Express ?

**Definition simple :**
Express est une bibliotheque JavaScript qui simplifie la creation de serveurs web et d'API avec Node.js.

### Pourquoi utiliser Express ?

**Sans Express (code complique) :**
```javascript
// Code long et complique pour creer un serveur
const http = require('http');
const server = http.createServer((req, res) => {
  if (req.url === '/api/equipes' && req.method === 'GET') {
    // Beaucoup de code...
  }
});
```

**Avec Express (code simple) :**
```javascript
// Code court et simple
const express = require('express');
const app = express();

app.get('/api/equipes', (req, res) => {
  res.json({ message: 'Liste des equipes' });
});
```

### Le role d'Express

Express fait 3 choses principales :

1. **Cree un serveur web facilement**
   ```javascript
   const app = express();
   app.listen(3000); // Serveur sur le port 3000
   ```

2. **Definit des routes (URL) simplement**
   ```javascript
   app.get('/api/equipes', ...);    // Route GET
   app.post('/api/equipes', ...);   // Route POST
   app.put('/api/equipes/:id', ...); // Route PUT
   app.delete('/api/equipes/:id', ...); // Route DELETE
   ```

3. **Gere les requetes et reponses automatiquement**
   ```javascript
   app.get('/api/equipes', (req, res) => {
     res.json({ data: equipes }); // Renvoie du JSON
   });
   ```

### Les middlewares dans Express

**Qu'est-ce qu'un middleware ?**

Un middleware est une fonction qui s'execute ENTRE la requete et la reponse.

**Schema :**
```
Client ----> Middleware 1 ----> Middleware 2 ----> Route ----> Reponse
             (Logger)            (Parser JSON)       (GET /api/equipes)
```

**Exemple concret :**
```javascript
// Middleware pour parser le JSON
app.use(express.json());

// Middleware pour autoriser CORS
app.use(cors());

// Middleware pour logger les requetes
app.use((req, res, next) => {
  console.log(`${req.method} ${req.url}`);
  next(); // Passe au middleware suivant
});
```

---

## 4. ARCHITECTURE DES APPLICATIONS

### Application Monolithique

**Definition :**
Une application ou tout le code est dans un seul programme.

**Schema :**
```
┌─────────────────────────────────────┐
│     APPLICATION MONOLITHIQUE        │
│                                     │
│  ┌──────────────────────────────┐   │
│  │   Interface Utilisateur      │   │
│  ├──────────────────────────────┤   │
│  │   Logique Metier             │   │
│  ├──────────────────────────────┤   │
│  │   Base de Donnees            │   │
│  └──────────────────────────────┘   │
│                                     │
│    Tout est dans un seul bloc       │
└─────────────────────────────────────┘
```

**Avantages :**
- Simple a developper au debut
- Facile a deployer (un seul fichier)

**Inconvenients :**
- Difficile a maintenir quand ca grossit
- Si une partie plante, tout plante
- Difficile a faire evoluer

### Architecture Microservices

**Definition :**
L'application est divisee en plusieurs petits services independants qui communiquent via des API.

**Schema :**
```
┌─────────────┐     ┌─────────────┐     ┌─────────────┐
│   SERVICE   │     │   SERVICE   │     │   SERVICE   │
│  EQUIPES    │◄───►│   MARKET    │◄───►│   USERS     │
│             │     │             │     │             │
│  API REST   │     │  API REST   │     │  API REST   │
└─────────────┘     └─────────────┘     └─────────────┘
       ▲                   ▲                   ▲
       │                   │                   │
       └───────────────────┴───────────────────┘
                           │
                    ┌──────────────┐
                    │   CLIENT     │
                    │  (Frontend)  │
                    └──────────────┘
```

**Avantages :**
- Chaque service peut etre modifie independamment
- Si un service plante, les autres continuent
- Plusieurs equipes peuvent travailler en parallele
- Facile a faire evoluer (scalabilite)

**Inconvenients :**
- Plus complexe a mettre en place
- Necessite de gerer la communication entre services

### Role de l'API REST dans les deux architectures

#### Dans une application Monolithique

**Schema :**
```
┌────────────────────────────────────────┐
│      APPLICATION MONOLITHIQUE          │
│                                        │
│  ┌──────────────────────────────────┐  │
│  │         API REST                 │  │
│  │  (Interface de communication)    │  │
│  └──────────────────────────────────┘  │
│              ▲                         │
│              │                         │
│  ┌──────────────────────────────────┐  │
│  │      Logique Metier              │  │
│  └──────────────────────────────────┘  │
│              ▲                         │
│              │                         │
│  ┌──────────────────────────────────┐  │
│  │      Base de Donnees             │  │
│  └──────────────────────────────────┘  │
└────────────────────────────────────────┘
         ▲
         │
    ┌────────┐
    │ Client │
    └────────┘
```

**Role de l'API :**
- Point d'entree unique pour les clients
- Separe le frontend du backend
- Permet a plusieurs clients (web, mobile) d'utiliser la meme application

#### Dans une architecture Microservices

**Schema :**
```
                    ┌────────────┐
                    │  CLIENT    │
                    │ (Frontend) │
                    └─────┬──────┘
                          │
              ┌───────────┼───────────┐
              │           │           │
              ▼           ▼           ▼
        ┌─────────┐ ┌─────────┐ ┌─────────┐
        │   API   │ │   API   │ │   API   │
        │ EQUIPES │ │ MARKET  │ │  USERS  │
        └────┬────┘ └────┬────┘ └────┬────┘
             │           │           │
        ┌────▼────┐ ┌────▼────┐ ┌────▼────┐
        │   DB    │ │   DB    │ │   DB    │
        │ Equipes │ │ Market  │ │  Users  │
        └─────────┘ └─────────┘ └─────────┘
```

**Role de l'API :**
- Chaque service expose sa propre API REST
- Les services communiquent entre eux via API
- Independance totale entre les services
- Chaque API peut evoluer separement

### Exemple concret : Site de e-commerce

**Version Monolithique :**
```
┌────────────────────────────────┐
│      Application E-Commerce    │
│                                │
│  - Gestion produits            │
│  - Gestion commandes           │
│  - Gestion utilisateurs        │
│  - Gestion paiements           │
│  - Gestion stock               │
│                                │
│  Tout dans un seul programme   │
└────────────────────────────────┘
```

**Version Microservices :**
```
API Produits  ◄─┐
API Commandes ◄─┼─► Client Web
API Users     ◄─┤
API Paiements ◄─┤
API Stock     ◄─┘
```

Chaque API est un service independant avec sa propre base de donnees.

---

# PARTIE 2 : TRAVAUX DIRIGES (TD) {#partie2}

## OBJECTIF DU TD

Creer une API REST complete pour gerer des equipes de football et des donnees de marche boursier.

## SCHEMA DE L'ARCHITECTURE

```
┌───────────────────────────────────────────────────────────── ┐
│                        CLIENT (Navigateur)                   │
│                                                              │
│  ┌────────────┐              ┌─────────────────────┐         │
│  │ index.html │◄────────────►│   script.js         │         │
│  │ (Interface)│              │ (Logique client)    │         │
│  └────────────┘              └─────────────────────┘         │
└───────────────────────────┬──────────────────────────────────┘
                            │
                            │ HTTP Requests
                            │ (GET, POST, PUT, DELETE)
                            │
┌───────────────────────────▼──────────────────────────────────┐
│                      SERVEUR (Node.js + Express)             │
│                                                              │
│  ┌────────────────────────────────────────────────────┐      │
│  │                    app.js                          │      │
│  │              (Serveur principal)                   │      │
│  └───────────────────┬────────────────────────────────┘      │
│                      │                                       │
│         ┌────────────┼────────────┐                          │
│         │            │            │                          │
│         ▼            ▼            ▼                          │
│  ┌──────────┐ ┌──────────┐ ┌──────────┐                      │
│  │  Route   │ │  Route   │ │  Route   │                      │
│  │ /equipes │ │ /market  │ │  /docs   │                      │
│  └────┬─────┘ └────┬─────┘ └──────────┘                      │
│       │            │                                         │
│       ▼            ▼                                         │
│  equipesRoutes  marketRoutes                                 │
│       │            │                                         │
└───────┼────────────┼───────────────────────────────────────  ┘
        │            │
        ▼            ▼
┌─────────────────────────────────┐
│      DONNEES (Fichiers JSON)    │
│                                 │
│  ┌──────────────────────────┐   │
│  │   equipes.json           │   │
│  │   [                      │   │
│  │     {id:1, name:"..."}   │   │
│  │   ]                      │   │
│  └──────────────────────────┘   │
│                                 │
│  ┌──────────────────────────┐   │
│  │   marketData.json        │   │
│  │   [                      │   │
│  │     {id:101, key:"..."}  │   │
│  │   ]                      │   │
│  └──────────────────────────┘   │
└─────────────────────────────────┘
```

## PRINCIPE DE FONCTIONNEMENT

### Flux d'une requete GET

```
1. L'utilisateur clique sur "Voir les equipes" dans index.html
        │
        ▼
2. script.js envoie une requete GET /api/equipes
        │
        ▼
3. Le serveur (app.js) recoit la requete
        │
        ▼
4. La route /api/equipes dans equipesRoutes.js est activee
        │
        ▼
5. Le code lit le fichier equipes.json
        │
        ▼
6. Les donnees sont renvoyees en JSON
        │
        ▼
7. script.js recoit les donnees
        │
        ▼
8. index.html affiche les equipes a l'utilisateur
```

### Flux d'une requete POST

```
1. L'utilisateur remplit le formulaire dans index.html
        │
        ▼
2. script.js envoie une requete POST /api/equipes
   avec les donnees : {name: "PSG", country: "France"}
        │
        ▼
3. Le serveur valide les donnees
        │
        ▼
4. Si valide : ajoute l'equipe dans equipes.json
   Si invalide : renvoie une erreur 400
        │
        ▼
5. Reponse envoyee au client
        │
        ▼
6. script.js affiche le resultat
```

---

## ETAPES DE CREATION (PAS A PAS)

### ETAPE 1 : Preparation du projet

**1.1 Creer le dossier du projet**
```bash
mkdir mon-api-rest
cd mon-api-rest
```

**1.2 Initialiser npm**
```bash
npm init -y
```
Cela cree un fichier `package.json`

**1.3 Installer Express et CORS**
```bash
npm install express cors
```

**1.4 Creer la structure des dossiers**
```bash
mkdir data routes public
```

Structure finale :
```
mon-api-rest/
├── data/           (fichiers JSON)
├── routes/         (fichiers des routes)
├── public/         (fichiers HTML/CSS/JS)
├── app.js          (serveur principal)
└── package.json    (configuration)
```

---

### ETAPE 2 : Creer les fichiers de donnees

**2.1 Creer `data/equipes.json`**
```json
[
  {
    "id": 1,
    "name": "Real Madrid",
    "country": "Spain",
    "createdAt": "2025-01-01T10:00:00.000Z"
  },
  {
    "id": 2,
    "name": "Manchester City",
    "country": "England",
    "createdAt": "2025-01-01T10:00:00.000Z"
  }
]
```

**2.2 Creer `data/marketData.json`**
```json
[
  {
    "id": 101,
    "key": "AAPL",
    "value": 150.50,
    "status": "active",
    "createdAt": "2025-01-01T10:00:00.000Z"
  },
  {
    "id": 102,
    "key": "GOOGL",
    "value": 2800.10,
    "status": "active",
    "createdAt": "2025-01-01T10:00:00.000Z"
  }
]
```

---

### ETAPE 3 : Creer le serveur principal

**Creer `app.js`**

```javascript
// 1. IMPORTER LES MODULES
const express = require('express');
const cors = require('cors');

// 2. CREER L'APPLICATION
const app = express();
const port = 3000;

// 3. CONFIGURER LES MIDDLEWARES
app.use(express.json());           // Pour lire le JSON
app.use(cors());                   // Pour autoriser les requetes
app.use(express.static('public')); // Pour servir les fichiers HTML

// 4. ROUTE DE TEST
app.get('/', (req, res) => {
  res.json({
    message: 'Bienvenue sur l\'API REST',
    endpoints: {
      equipes: '/api/equipes',
      market: '/api/market'
    }
  });
});

// 5. DEMARRER LE SERVEUR
app.listen(port, () => {
  console.log(`Serveur demarre sur http://localhost:${port}`);
});
```

**Test : Demarrer le serveur**
```bash
node app.js
```

Ouvrir `http://localhost:3000` dans le navigateur.
Vous devez voir le message JSON.

---

### ETAPE 4 : Creer les routes pour les equipes

**Creer `routes/equipesRoutes.js`**

```javascript
const express = require('express');
const router = express.Router();
const fs = require('fs');
const path = require('path');

// Chemin vers le fichier JSON
const fichierEquipes = path.join(__dirname, '../data/equipes.json');

// FONCTION : Lire les equipes
function lireEquipes() {
  const data = fs.readFileSync(fichierEquipes, 'utf8');
  return JSON.parse(data);
}

// FONCTION : Ecrire les equipes
function ecrireEquipes(equipes) {
  fs.writeFileSync(fichierEquipes, JSON.stringify(equipes, null, 2));
}

// ROUTE 1 : GET - Lister toutes les equipes
router.get('/', (req, res) => {
  const equipes = lireEquipes();
  res.json({
    success: true,
    count: equipes.length,
    data: equipes
  });
});

// ROUTE 2 : GET - Une equipe par ID
router.get('/:id', (req, res) => {
  const id = parseInt(req.params.id);
  const equipes = lireEquipes();
  const equipe = equipes.find(e => e.id === id);
  
  if (!equipe) {
    return res.status(404).json({
      success: false,
      message: 'Equipe non trouvee'
    });
  }
  
  res.json({
    success: true,
    data: equipe
  });
});

// ROUTE 3 : POST - Creer une equipe
router.post('/', (req, res) => {
  const { name, country } = req.body;
  
  // Validation
  if (!name || !country) {
    return res.status(400).json({
      success: false,
      message: 'Le nom et le pays sont requis'
    });
  }
  
  const equipes = lireEquipes();
  
  // Creer le nouvel ID
  const nouvelId = equipes.length > 0 
    ? Math.max(...equipes.map(e => e.id)) + 1 
    : 1;
  
  // Creer la nouvelle equipe
  const nouvelleEquipe = {
    id: nouvelId,
    name,
    country,
    createdAt: new Date().toISOString()
  };
  
  equipes.push(nouvelleEquipe);
  ecrireEquipes(equipes);
  
  res.status(201).json({
    success: true,
    message: 'Equipe creee',
    data: nouvelleEquipe
  });
});

// ROUTE 4 : PUT - Modifier une equipe
router.put('/:id', (req, res) => {
  const id = parseInt(req.params.id);
  const { name, country } = req.body;
  
  const equipes = lireEquipes();
  const index = equipes.findIndex(e => e.id === id);
  
  if (index === -1) {
    return res.status(404).json({
      success: false,
      message: 'Equipe non trouvee'
    });
  }
  
  equipes[index] = {
    ...equipes[index],
    name,
    country,
    updatedAt: new Date().toISOString()
  };
  
  ecrireEquipes(equipes);
  
  res.json({
    success: true,
    message: 'Equipe modifiee',
    data: equipes[index]
  });
});

// ROUTE 5 : DELETE - Supprimer une equipe
router.delete('/:id', (req, res) => {
  const id = parseInt(req.params.id);
  
  const equipes = lireEquipes();
  const index = equipes.findIndex(e => e.id === id);
  
  if (index === -1) {
    return res.status(404).json({
      success: false,
      message: 'Equipe non trouvee'
    });
  }
  
  const equipeSupprimee = equipes[index];
  equipes.splice(index, 1);
  ecrireEquipes(equipes);
  
  res.json({
    success: true,
    message: 'Equipe supprimee',
    data: equipeSupprimee
  });
});

module.exports = router;
```

**Ajouter les routes dans `app.js`**

Apres les middlewares, ajoutez :
```javascript
// IMPORTER LES ROUTES
const equipesRouter = require('./routes/equipesRoutes');

// UTILISER LES ROUTES
app.use('/api/equipes', equipesRouter);
```

**Test des routes**

Redemarrer le serveur et tester dans le navigateur :
- `http://localhost:3000/api/equipes` - Voir toutes les equipes

---

### ETAPE 5 : Creer l'interface HTML

**Creer `public/index.html`**

```html
<!DOCTYPE html>
<html lang="fr">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>API REST - Interface</title>
  <style>
    * {
      margin: 0;
      padding: 0;
      box-sizing: border-box;
    }
    
    body {
      font-family: Arial, sans-serif;
      background: #f5f5f5;
      padding: 20px;
    }
    
    .container {
      max-width: 1000px;
      margin: 0 auto;
      background: white;
      padding: 30px;
      border-radius: 8px;
      box-shadow: 0 2px 10px rgba(0,0,0,0.1);
    }
    
    h1 {
      color: #333;
      margin-bottom: 30px;
      text-align: center;
    }
    
    .section {
      margin-bottom: 40px;
    }
    
    .section h2 {
      color: #555;
      margin-bottom: 20px;
      border-bottom: 2px solid #007bff;
      padding-bottom: 10px;
    }
    
    .form-group {
      margin-bottom: 15px;
    }
    
    label {
      display: block;
      margin-bottom: 5px;
      color: #666;
      font-weight: bold;
    }
    
    input, select {
      width: 100%;
      padding: 10px;
      border: 1px solid #ddd;
      border-radius: 4px;
      font-size: 14px;
    }
    
    button {
      padding: 10px 20px;
      background: #007bff;
      color: white;
      border: none;
      border-radius: 4px;
      cursor: pointer;
      font-size: 14px;
      margin-right: 10px;
    }
    
    button:hover {
      background: #0056b3;
    }
    
    .btn-success { background: #28a745; }
    .btn-success:hover { background: #218838; }
    
    .btn-danger { background: #dc3545; }
    .btn-danger:hover { background: #c82333; }
    
    #resultat {
      background: #f8f9fa;
      padding: 20px;
      border-radius: 4px;
      border: 1px solid #dee2e6;
      min-height: 200px;
      font-family: 'Courier New', monospace;
      font-size: 13px;
      white-space: pre-wrap;
    }
    
    .equipes-list {
      display: grid;
      grid-template-columns: repeat(auto-fill, minmax(250px, 1fr));
      gap: 20px;
      margin-top: 20px;
    }
    
    .equipe-card {
      background: #f8f9fa;
      padding: 15px;
      border-radius: 4px;
      border: 1px solid #dee2e6;
    }
    
    .equipe-card h3 {
      margin-bottom: 10px;
      color: #007bff;
    }
    
    .equipe-card p {
      margin: 5px 0;
      color: #666;
    }
  </style>
</head>
<body>
  <div class="container">
    <h1>Interface de Test - API REST</h1>
    
    <!-- SECTION 1 : AFFICHER LES EQUIPES -->
    <div class="section">
      <h2>1. Afficher les Equipes</h2>
      <button onclick="afficherEquipes()" class="btn-success">
        Charger les equipes
      </button>
      <div id="listeEquipes" class="equipes-list"></div>
    </div>
    
    <!-- SECTION 2 : CREER UNE EQUIPE -->
    <div class="section">
      <h2>2. Creer une Equipe</h2>
      <div class="form-group">
        <label>Nom de l'equipe :</label>
        <input type="text" id="nom" placeholder="Ex: Paris Saint-Germain">
      </div>
      <div class="form-group">
        <label>Pays :</label>
        <input type="text" id="pays" placeholder="Ex: France">
      </div>
      <button onclick="creerEquipe()" class="btn-success">
        Creer l'equipe
      </button>
    </div>
    
    <!-- SECTION 3 : CHERCHER UNE EQUIPE -->
    <div class="section">
      <h2>3. Chercher une Equipe par ID</h2>
      <div class="form-group">
        <label>ID de l'equipe :</label>
        <input type="number" id="idRecherche" placeholder="Ex: 1">
      </div>
      <button onclick="chercherEquipe()">
        Rechercher
      </button>
    </div>
    
    <!-- SECTION 4 : RESULTAT -->
    <div class="section">
      <h2>4. Resultat de l'operation</h2>
      <div id="resultat">Aucune operation effectuee</div>
    </div>
  </div>
  
  <script src="script.js"></script>
</body>
</html>
```

**Creer `public/script.js`**

```javascript
// URL de base de l'API
const API_URL = 'http://localhost:3000/api';

// Element pour afficher les resultats
const resultat = document.getElementById('resultat');

// FONCTION 1 : Afficher toutes les equipes
async function afficherEquipes() {
  try {
    // Envoyer la requete GET
    const response = await fetch(`${API_URL}/equipes`);
    const data = await response.json();
    
    // Afficher dans la console (pour debug)
    console.log('Equipes recues:', data);
    
    // Afficher dans le resultat
    resultat.textContent = JSON.stringify(data, null, 2);
    
    // Afficher les cartes d'equipes
    const listeEquipes = document.getElementById('listeEquipes');
    listeEquipes.innerHTML = '';
    
    if (data.success && data.data.length > 0) {
      data.data.forEach(equipe => {
        const card = document.createElement('div');
        card.className = 'equipe-card';
        card.innerHTML = `
          <h3>${equipe.name}</h3>
          <p><strong>Pays:</strong> ${equipe.country}</p>
          <p><strong>ID:</strong> ${equipe.id}</p>
        `;
        listeEquipes.appendChild(card);
      });
    }
  } catch (error) {
    console.error('Erreur:', error);
    resultat.textContent = 'Erreur : ' + error.message;
  }
}

// FONCTION 2 : Creer une equipe
async function creerEquipe() {
  // Recuperer les valeurs des champs
  const nom = document.getElementById('nom').value;
  const pays = document.getElementById('pays').value;
  
  // Validation
  if (!nom || !pays) {
    alert('Veuillez remplir tous les champs');
    return;
  }
  
  try {
    // Envoyer la requete POST
    const response = await fetch(`${API_URL}/equipes`, {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json'
      },
      body: JSON.stringify({
        name: nom,
        country: pays
      })
    });
    
    const data = await response.json();
    
    // Afficher le resultat
    resultat.textContent = JSON.stringify(data, null, 2);
    
    // Vider les champs
    document.getElementById('nom').value = '';
    document.getElementById('pays').value = '';
    
    // Recharger la liste des equipes
    afficherEquipes();
    
  } catch (error) {
    console.error('Erreur:', error);
    resultat.textContent = 'Erreur : ' + error.message;
  }
}

// FONCTION 3 : Chercher une equipe par ID
async function chercherEquipe() {
  const id = document.getElementById('idRecherche').value;
  
  if (!id) {
    alert('Veuillez entrer un ID');
    return;
  }
  
  try {
    const response = await fetch(`${API_URL}/equipes/${id}`);
    const data = await response.json();
    
    resultat.textContent = JSON.stringify(data, null, 2);
    
  } catch (error) {
    console.error('Erreur:', error);
    resultat.textContent = 'Erreur : ' + error.message;
  }
}

// Charger les equipes au demarrage de la page
window.addEventListener('load', () => {
  afficherEquipes();
});
```

---

## RESUME : Que fait chaque fichier ?

| Fichier | Role | Ce qu'il fait |
|---------|------|---------------|
| `app.js` | Serveur principal | Demarre le serveur, configure les middlewares |
| `routes/equipesRoutes.js` | Routes des equipes | Gere toutes les operations CRUD pour les equipes |
| `data/equipes.json` | Donnees | Stocke les equipes |
| `public/index.html` | Interface visuelle | Affiche l'interface pour l'utilisateur |
| `public/script.js` | Logique client | Envoie les requetes a l'API et affiche les resultats |

---

## TESTER VOTRE API

### Test 1 : Avec le navigateur

1. Demarrer le serveur : `node app.js`
2. Ouvrir : `http://localhost:3000`
3. Cliquer sur "Charger les equipes"
4. Voir les equipes s'afficher

### Test 2 : Creer une equipe

1. Remplir le formulaire
2. Cliquer sur "Creer l'equipe"
3. Voir le resultat dans la section "Resultat"
4. Les equipes se rechargent automatiquement

### Test 3 : Avec Postman

1. Ouvrir Postman
2. Nouvelle requete GET : `http://localhost:3000/api/equipes`
3. Cliquer sur "Send"
4. Voir la reponse JSON

---

## CONCLUSION

Vous avez maintenant une API REST complete et fonctionnelle !

**Ce que vous avez appris :**
- Creer un serveur avec Express
- Definir des routes (GET, POST, PUT, DELETE)
- Lire et ecrire dans des fichiers JSON
- Creer une interface HTML pour interagir avec l'API
- Comprendre le flux de donnees entre client et serveur

**Prochaines etapes possibles :**
- Ajouter une base de donnees (MongoDB, PostgreSQL)
- Ajouter l'authentification
- Deployer sur Internet
- Ajouter plus de fonctionnalites
