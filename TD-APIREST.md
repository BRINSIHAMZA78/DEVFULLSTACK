# DOCUMENTATION COMPLETE
# API REST avec Architecture Microservices et JWT
# Reference pour stagiaire 206

---

## TABLE DES MATIERES

1.  [Vue d ensemble du projet](#1-vue-densemble-du-projet)
2.  [Structure des fichiers](#2-structure-des-fichiers)
3.  [Installation pas a pas](#3-installation-pas-a-pas)
4.  [Fichier .env](#4-fichier-env)
5.  [Fichier package.json](#5-fichier-packagejson)
6.  [Base de donnees JSON](#6-base-de-donnees-json)
7.  [Fichier api.js — Serveur principal](#7-fichier-apijs--serveur-principal)
8.  [Auth Service — services/auth-service/server.js](#8-auth-service--servicesauth-serviceserverjs)
9.  [Tasks Service — services/tasks-service/server.js](#9-tasks-service--servicestasks-serviceserverjs)
10. [Interface web — public/index.html](#10-interface-web--publicindexhtml)
11. [Architecture Microservices expliquee](#11-architecture-microservices-expliquee)
12. [JWT explique de A a Z](#12-jwt-explique-de-a-a-z)
13. [bcrypt explique de A a Z](#13-bcrypt-explique-de-a-a-z)
14. [Les Middlewares expliques](#14-les-middlewares-expliques)
15. [CORS explique](#15-cors-explique)
16. [Tester l application](#16-tester-lapplication)
17. [Glossaire complet](#17-glossaire-complet)
18. [Questions de validation](#18-questions-de-validation)

---

## 1. VUE D ENSEMBLE DU PROJET

### Ce que fait cette application

Cette application est un gestionnaire de taches (To-Do List) avec :
- Inscription et connexion des utilisateurs
- Chaque utilisateur voit UNIQUEMENT ses propres taches
- Securite par token JWT
- Mots de passe proteges par bcrypt

### Architecture : 3 serveurs qui travaillent ensemble

```
┌─────────────────────────────────────────────────────────────┐
│                    NAVIGATEUR (Eleve)                       │
│                  http://localhost:3000                       │
└──────────────┬──────────────────┬───────────────────────────┘
               │                  │
               │ Login/Register   │ Gestion des taches
               ▼                  ▼
┌──────────────────────┐  ┌──────────────────────────────────┐
│    AUTH SERVICE      │  │         TASKS SERVICE            │
│    Port : 3001       │  │         Port : 3002              │
│                      │  │                                  │
│  POST /auth/register │  │  GET    /tasks                   │
│  POST /auth/login    │  │  POST   /tasks                   │
│                      │  │  PATCH  /tasks/:id               │
│  Repond avec         │  │  DELETE /tasks/:id               │
│  un TOKEN JWT        │  │                                  │
└──────────┬───────────┘  └──────────────┬───────────────────┘
           │                             │
           │        ┌────────────────────┘
           ▼        ▼
┌──────────────────────────────────────┐
│         BASE DE DONNEES JSON         │
│         dossier data/                │
│                                      │
│  users.json  → Liste des utilisateurs│
│  tasks.json  → Liste des taches      │
└──────────────────────────────────────┘
```

### Difference entre Monolithique et Microservices

```
APPLICATION MONOLITHIQUE          APPLICATION MICROSERVICES
─────────────────────────         ──────────────────────────
Un seul fichier : api.js          3 serveurs independants
Un seul port : 3000               Ports : 3000, 3001, 3002
Si une partie plante :            Si Auth plante :
  → Tout s arrete                   → Tasks continue
Difficile a scaler                Chaque service scalable
```

---

## 2. STRUCTURE DES FICHIERS

```
apirestexercice/
│
├── api.js                          ← Serveur web (port 3000) : sert l IHM
│
├── services/                       ← Dossier des microservices
│   ├── auth-service/
│   │   └── server.js               ← Auth Service (port 3001)
│   └── tasks-service/
│       └── server.js               ← Tasks Service (port 3002)
│
├── data/                           ← Base de donnees JSON partagee
│   ├── users.json                  ← Stocke les utilisateurs
│   └── tasks.json                  ← Stocke les taches
│
├── public/                         ← Fichiers envoyes au navigateur
│   └── index.html                  ← Interface web (IHM)
│
├── .env                            ← Variables secretes (cle JWT, port)
├── .gitignore                      ← Fichiers exclus de Git
├── package.json                    ← Configuration npm et dependances
├── README.md                       ← Guide de demarrage rapide
├── COURS.md                        ← Cours complet Node.js / Express / JWT
└── DOCUMENTATION.md                ← Ce fichier
```

### Role de chaque fichier

| Fichier | Port | Responsabilite |
|---------|------|---------------|
| `api.js` | 3000 | Sert uniquement l interface web HTML |
| `auth-service/server.js` | 3001 | Inscription + Connexion + Creation JWT |
| `tasks-service/server.js` | 3002 | CRUD des taches + Verification JWT |
| `data/users.json` | — | Base de donnees des utilisateurs |
| `data/tasks.json` | — | Base de donnees des taches |
| `.env` | — | Cle secrete JWT et configuration |

---

## 3. INSTALLATION PAS A PAS

### Etape 1 : Verifier Node.js

```powershell
# Verifier que Node.js est installe
node --version
# Resultat attendu : v18.x.x ou superieur

# Verifier que npm est installe
npm --version
# Resultat attendu : 9.x.x ou superieur
```

### Etape 2 : Ouvrir le projet

```powershell
# Aller dans le dossier du projet
cd C:\chemin\vers\apirestexercice
```

### Etape 3 : Installer les dependances

```powershell
# Installer tous les packages listes dans package.json
npm install
```

Cette commande telecharge et installe :
- `express` : framework web
- `jsonwebtoken` : gestion des tokens JWT
- `bcryptjs` : hachage des mots de passe
- `dotenv` : lecture du fichier .env
- `concurrently` : lancer plusieurs serveurs en meme temps
- `nodemon` : redemarrage automatique en developpement

### Etape 4 : Verifier le fichier .env

Verifier que le fichier `.env` existe a la racine du projet avec ce contenu :

```
SECRET_KEY=ceci_est_ma_cle_secrete_tres_longue
PORT=3000
```

### Etape 5 : Demarrer l application

```powershell
# Lancer les 3 serveurs en meme temps (RECOMMANDE)
npm run start:all
```

Resultat dans le terminal :

```
 Serveur web     → http://localhost:3000  (interface web)

 Auth Service    → http://localhost:3001
   POST /auth/register  (inscription)
   POST /auth/login     (connexion)

 Tasks Service   → http://localhost:3002
   GET    /tasks         (lire les taches)
   POST   /tasks         (creer une tache)
   PATCH  /tasks/:id     (modifier une tache)
   DELETE /tasks/:id     (supprimer une tache)
```

### Etape 6 : Ouvrir l interface web

Ouvrir le navigateur et aller sur : **http://localhost:3000**

---

## 4. FICHIER .env

### A quoi ca sert ?

Le fichier `.env` contient des informations **secretes** qui ne doivent JAMAIS etre mises dans le code source ni envoyees sur Git.

### Contenu du fichier

```
# Cle secrete pour signer et verifier les tokens JWT
# IMPORTANT : cette cle doit etre longue et complexe en production
SECRET_KEY=ceci_est_ma_cle_secrete_tres_longue_12345

# Port du serveur web principal
PORT=3000
```

### Comment l utiliser dans le code

```javascript
// Charger le fichier .env (toujours en premiere ligne du fichier)
require('dotenv').config();

// Lire une variable
const cle = process.env.SECRET_KEY;  // "ceci_est_ma_cle_secrete..."
const port = process.env.PORT;       // "3000"
```

### Pourquoi ne pas ecrire la cle directement dans le code ?

```javascript
// MAUVAIS : la cle est visible dans le code source
const token = jwt.sign({ userId: 1 }, "maclesecrete");

// BON : la cle est cachee dans .env
const token = jwt.sign({ userId: 1 }, process.env.SECRET_KEY);
```

### Le fichier .gitignore

Le fichier `.gitignore` empeche d envoyer les fichiers sensibles sur Git :

```
node_modules/    ← Trop volumineux (telecharge avec npm install)
.env             ← Contient des secrets
```

---

## 5. FICHIER package.json

```json
{
  "name": "api-jwt-simple",
  "version": "1.0.0",
  "description": "API REST avec Microservices et JWT",
  "main": "api.js",
  "scripts": {
    "start":       "node api.js",
    "dev":         "nodemon api.js",
    "start:auth":  "node services/auth-service/server.js",
    "start:tasks": "node services/tasks-service/server.js",
    "start:all":   "concurrently \"npm run start\" \"npm run start:auth\" \"npm run start:tasks\"",
    "dev:auth":    "nodemon services/auth-service/server.js",
    "dev:tasks":   "nodemon services/tasks-service/server.js",
    "dev:all":     "concurrently \"npm run dev\" \"npm run dev:auth\" \"npm run dev:tasks\""
  },
  "dependencies": {
    "express":       "^4.18.2",
    "jsonwebtoken":  "^9.0.2",
    "bcryptjs":      "^2.4.3",
    "dotenv":        "^16.3.1"
  },
  "devDependencies": {
    "nodemon":       "^3.0.1",
    "concurrently":  "^8.2.2"
  }
}
```

### Explication des scripts

| Commande | Ce qu elle fait |
|----------|----------------|
| `npm start` | Lance `api.js` sur le port 3000 |
| `npm run dev` | Lance `api.js` avec nodemon (redemarrage auto) |
| `npm run start:auth` | Lance Auth Service sur le port 3001 |
| `npm run start:tasks` | Lance Tasks Service sur le port 3002 |
| `npm run start:all` | Lance les 3 serveurs en meme temps |
| `npm run dev:all` | Lance les 3 serveurs avec nodemon |

### Difference dependencies / devDependencies

```
dependencies    → Necessaires pour faire tourner l app en production
                  (express, jwt, bcrypt, dotenv)

devDependencies → Utiles seulement pendant le developpement
                  (nodemon, concurrently)
```

---

## 6. BASE DE DONNEES JSON

### Pourquoi des fichiers JSON ?

Pour ce projet pedagogique, on utilise des fichiers JSON a la place d une vraie
base de donnees (MySQL, MongoDB...). C est simple a comprendre et a lire.

### data/users.json

Ce fichier contient la liste de tous les utilisateurs inscrits.

```json
[
  {
    "id": 1,
    "email": "alice@email.com",
    "password": "$2a$10$N9qo8uLOickgx2ZMRZoMyeQi8Ke..."
  },
  {
    "id": 2,
    "email": "bob@email.com",
    "password": "$2a$10$xyz789abc123..."
  }
]
```

ATTENTION : Le champ `password` contient le HASH bcrypt, jamais le vrai mot de passe.

### data/tasks.json

Ce fichier contient la liste de toutes les taches de tous les utilisateurs.

```json
[
  {
    "id": 1,
    "userId": 1,
    "titre": "Apprendre les microservices",
    "completed": false
  },
  {
    "id": 2,
    "userId": 1,
    "titre": "Faire les courses",
    "completed": true
  },
  {
    "id": 3,
    "userId": 2,
    "titre": "Tache de Bob",
    "completed": false
  }
]
```

Le champ `userId` lie chaque tache a son proprietaire.
Alice (userId=1) ne voit que les taches 1 et 2.
Bob (userId=2) ne voit que la tache 3.

---

## 7. FICHIER api.js — Serveur principal

### Role

Ce fichier lance un serveur Express sur le port 3000.
Son unique responsabilite : servir l interface web (le fichier `public/index.html`).
Il ne gere PAS l authentification ni les taches.

### Code complet avec explications

```javascript
// ============================================
// api.js — SERVEUR PRINCIPAL (PORT 3000)
// Role : servir l interface web au navigateur
// ============================================

// Charger les variables d environnement depuis .env
// process.env.PORT sera disponible apres cette ligne
require('dotenv').config();

// Importer Express : framework web pour Node.js
const express = require('express');

// Importer path : module natif Node.js pour les chemins de fichiers
// path.join() cree des chemins compatibles Windows et Linux
const path = require('path');

// Importer les fichiers de routes
// Ces fichiers definissent les endpoints /auth et /tasks
const authRoutes  = require('./routes/authRoutes');
const taskRoutes  = require('./routes/taskRoutes');

// Creer l instance Express
// app est l objet principal sur lequel on branche tout
const app = express();

// ---- MIDDLEWARES ----

// Middleware 1 : lire le JSON des requetes POST/PATCH
// Sans ca, req.body serait undefined
app.use(express.json());

// Middleware 2 : servir les fichiers statiques du dossier public/
// Quand le navigateur demande http://localhost:3000/
// Express renvoie automatiquement public/index.html
app.use(express.static(path.join(__dirname, 'public')));

// ---- ROUTES ----
// Ces routes sont gardees pour la compatibilite monolithique
app.use('/auth',  authRoutes);
app.use('/tasks', taskRoutes);

// ---- DEMARRER LE SERVEUR ----
const PORT = process.env.PORT || 3000;

app.listen(PORT, () => {
  console.log(`\n Serveur web → http://localhost:${PORT}`);
  console.log(`   Ouvrez votre navigateur sur http://localhost:${PORT}\n`);
});
```

---

## 8. AUTH SERVICE — services/auth-service/server.js

### Role

Ce microservice gere UNIQUEMENT :
- L inscription (creation d un compte)
- La connexion (verification des identifiants + creation du JWT)

Il ne sait rien des taches. Il ne communique pas avec Tasks Service.

### Code complet avec explications

```javascript
// =======================================================================
// MICROSERVICE AUTH - PORT 3001
// =======================================================================
// Responsabilite : Inscription et Connexion uniquement.
// Routes :
//   POST http://localhost:3001/auth/register
//   POST http://localhost:3001/auth/login
// =======================================================================

// Charger .env (remonte 2 niveaux car on est dans services/auth-service/)
require('dotenv').config({ path: '../../.env' });

// Imports des dependances
const express = require('express');
const bcrypt  = require('bcryptjs');     // Hasher les mots de passe
const jwt     = require('jsonwebtoken'); // Creer les tokens JWT
const fs      = require('fs').promises;  // Lire/ecrire les fichiers JSON
const path    = require('path');

const app = express();

// ---- MIDDLEWARES ----

// Lire le JSON du body des requetes
app.use(express.json());

// CORS : autoriser les requetes du navigateur (port 3000 → port 3001)
// Sans ca, le navigateur bloquerait les appels "cross-origin"
app.use((req, res, next) => {
  res.header('Access-Control-Allow-Origin', '*');
  res.header('Access-Control-Allow-Headers', 'Content-Type, Authorization');
  res.header('Access-Control-Allow-Methods', 'GET, POST, PATCH, DELETE');
  next();
});

// ---- BASE DE DONNEES ----
// Chemin absolu vers users.json a la racine du projet
const USERS_FILE = path.join(__dirname, '../../data/users.json');

// ---- FONCTIONS UTILITAIRES ----

// Lire tous les utilisateurs depuis le fichier JSON
async function getUsers() {
  const data = await fs.readFile(USERS_FILE, 'utf8');
  return JSON.parse(data); // Texte JSON → tableau JavaScript
}

// Sauvegarder les utilisateurs dans le fichier JSON
async function saveUsers(users) {
  // null, 2 = indentation de 2 espaces (fichier lisible)
  await fs.writeFile(USERS_FILE, JSON.stringify(users, null, 2));
}

// =======================================================================
// ROUTE 1 : POST /auth/register — INSCRIPTION
// =======================================================================
// Corps de la requete attendu :
//   { "email": "bob@email.com", "password": "monmotdepasse" }
//
// Reponses possibles :
//   201 Created        → { "message": "Utilisateur cree", "id": 2 }
//   400 Bad Request    → { "error": "Email deja utilise" }
//   500 Server Error   → { "error": "Erreur serveur" }
// =======================================================================
app.post('/auth/register', async (req, res) => {
  try {
    // Extraire les champs du body (grace au middleware express.json())
    const { email, password } = req.body;

    // Validation : les deux champs sont obligatoires
    if (!email || !password) {
      return res.status(400).json({ error: 'Email et mot de passe requis' });
    }

    // Charger les utilisateurs existants
    const users = await getUsers();

    // Verifier que l email n est pas deja utilise
    // find() retourne undefined si non trouve (= pas d email en double)
    if (users.find(u => u.email === email)) {
      return res.status(400).json({ error: 'Email deja utilise' });
    }

    // Hasher le mot de passe avec bcrypt
    // 10 = salt rounds (nombre de tours de chiffrement)
    // RESULTAT : "password123" → "$2a$10$N9qo8uLOickgx2ZMRZoMye..."
    // IMPOSSIBLE de retrouver "password123" depuis le hash
    const hashedPassword = await bcrypt.hash(password, 10);

    // Creer l objet utilisateur
    const newUser = {
      id: users.length + 1,   // ID auto-incremente
      email,                   // email: email (syntaxe courte ES6)
      password: hashedPassword // HASH stocke, jamais le vrai mot de passe
    };

    // Ajouter au tableau et sauvegarder
    users.push(newUser);
    await saveUsers(users);

    // 201 = Created : nouvelle ressource creee avec succes
    res.status(201).json({ message: 'Utilisateur cree', id: newUser.id });

  } catch (error) {
    // 500 = erreur inattendue cote serveur
    res.status(500).json({ error: 'Erreur serveur' });
  }
});

// =======================================================================
// ROUTE 2 : POST /auth/login — CONNEXION
// =======================================================================
// Corps de la requete attendu :
//   { "email": "alice@email.com", "password": "password123" }
//
// Reponses possibles :
//   200 OK           → { "message": "...", "token": "eyJhbG...", "userId": 1 }
//   401 Unauthorized → { "error": "Email ou mot de passe incorrect" }
//   500 Server Error → { "error": "Erreur serveur" }
// =======================================================================
app.post('/auth/login', async (req, res) => {
  try {
    const { email, password } = req.body;

    const users = await getUsers();

    // Chercher l utilisateur par email
    const user = users.find(u => u.email === email);

    // Si l email n existe pas dans la base
    // On dit "email OU mot de passe incorrect" → ne pas reveler si l email existe
    if (!user) {
      return res.status(401).json({ error: 'Email ou mot de passe incorrect' });
    }

    // Comparer le mot de passe saisi avec le hash stocke
    // bcrypt.compare() ne decode PAS le hash, il rehache et compare
    const validPassword = await bcrypt.compare(password, user.password);

    if (!validPassword) {
      return res.status(401).json({ error: 'Email ou mot de passe incorrect' });
    }

    // ---------------------------------------------------------------
    // CREATION DU TOKEN JWT
    // jwt.sign(payload, cleSecrete, options) :
    //
    //   payload    : donnees encodees dans le token
    //                → { userId: 1 }
    //                → NE PAS mettre le mot de passe ici !
    //
    //   cleSecrete : chaine connue seulement du serveur
    //                → depuis .env pour ne pas l exposer
    //
    //   options    : { expiresIn: '24h' }
    //                → le token sera invalide apres 24 heures
    //
    // RESULTAT :
    //   "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VySWQiOjF9.abc123"
    //    ←──────── HEADER ────────────────────→←── PAYLOAD ──→←─ SIGN ─→
    // ---------------------------------------------------------------
    const token = jwt.sign(
      { userId: user.id },       // Payload : donnees dans le token
      process.env.SECRET_KEY,    // Cle secrete depuis .env
      { expiresIn: '24h' }       // Expiration : 24 heures
    );

    // Envoyer le token au client
    // Le client DOIT stocker ce token et l envoyer dans chaque requete suivante
    // Header : "Authorization: Bearer eyJhbGci..."
    res.json({
      message: 'Connexion reussie',
      token,           // Token a stocker et transmettre
      userId: user.id  // Utile pour l interface
    });

  } catch (error) {
    res.status(500).json({ error: 'Erreur serveur' });
  }
});

// ---- DEMARRER LE SERVICE ----
const PORT = 3001;
app.listen(PORT, () => {
  console.log(`\n Auth Service    → http://localhost:${PORT}`);
  console.log(`   POST /auth/register  (inscription)`);
  console.log(`   POST /auth/login     (connexion)\n`);
});
```

---

## 9. TASKS SERVICE — services/tasks-service/server.js

### Role

Ce microservice gere UNIQUEMENT les taches (CRUD complet).
Il est independant d Auth Service MAIS il verifie les tokens JWT lui-meme
en utilisant la meme SECRET_KEY.

### Code complet avec explications

```javascript
// =======================================================================
// MICROSERVICE TASKS - PORT 3002
// =======================================================================
// Responsabilite : CRUD des taches.
// Toutes les routes sont protegees par JWT.
// Routes :
//   GET    http://localhost:3002/tasks
//   POST   http://localhost:3002/tasks
//   PATCH  http://localhost:3002/tasks/:id
//   DELETE http://localhost:3002/tasks/:id
// =======================================================================

require('dotenv').config({ path: '../../.env' });

const express = require('express');
const jwt     = require('jsonwebtoken'); // Verifier les tokens (pas les creer)
const fs      = require('fs').promises;
const path    = require('path');

const app = express();

// ---- MIDDLEWARES ----

app.use(express.json());

// CORS : autoriser les requetes du navigateur (port 3000 → port 3002)
app.use((req, res, next) => {
  res.header('Access-Control-Allow-Origin', '*');
  res.header('Access-Control-Allow-Headers', 'Content-Type, Authorization');
  res.header('Access-Control-Allow-Methods', 'GET, POST, PATCH, DELETE');
  next();
});

// ---- BASE DE DONNEES ----
const TASKS_FILE = path.join(__dirname, '../../data/tasks.json');

// ---- FONCTIONS UTILITAIRES ----

async function getTasks() {
  const data = await fs.readFile(TASKS_FILE, 'utf8');
  return JSON.parse(data);
}

async function saveTasks(tasks) {
  await fs.writeFile(TASKS_FILE, JSON.stringify(tasks, null, 2));
}

// =======================================================================
// MIDDLEWARE verifyToken — VERIFICATION DU TOKEN JWT
// =======================================================================
// S execute AVANT chaque route protegee.
// Si le token est absent ou invalide → bloque la requete.
// Si le token est valide → ajoute req.userId et passe a la route.
//
// FLUX :
//   Requete → verifyToken() → OK  → route → reponse
//                          → KO  → erreur 401/403 (bloque ici)
//
// POURQUOI Tasks Service verifie lui-meme ?
//   Independance des services. Chaque service peut fonctionner seul.
//   Les deux services partagent SECRET_KEY → ils peuvent tous deux
//   verifier les tokens crees par Auth Service.
// =======================================================================
function verifyToken(req, res, next) {

  // Lire le header Authorization
  // Client envoie : "Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."
  //
  // req.headers.authorization = "Bearer eyJhbGci..."
  // ?.split(' ')               = ["Bearer", "eyJhbGci..."]  (le ?. evite l erreur si undefined)
  // [1]                        = "eyJhbGci..."              (on prend juste le token)
  const token = req.headers.authorization?.split(' ')[1];

  // Pas de token fourni → refuser l acces
  if (!token) {
    // 401 Unauthorized : le client ne s est pas authentifie
    return res.status(401).json({ error: 'Token manquant' });
  }

  try {
    // Verifier ET decoder le token
    // jwt.verify() fait 2 choses :
    //   1. Verifie la SIGNATURE (le token n a pas ete modifie)
    //   2. Verifie l EXPIRATION (le token n est pas expire)
    // Si une verification echoue → exception → catch() → erreur 403
    const decoded = jwt.verify(token, process.env.SECRET_KEY);
    // decoded = { userId: 1, iat: 1745678900, exp: 1745765300 }

    // Ajouter userId a req pour que les routes puissent l utiliser
    req.userId = decoded.userId;

    // Token valide → passer a la route suivante
    next();

  } catch (error) {
    // 403 Forbidden : identifie mais token invalide ou expire
    return res.status(403).json({ error: 'Token invalide' });
  }
}

// =======================================================================
// ROUTE 1 : GET /tasks — LIRE toutes ses taches
// =======================================================================
// Headers requis : Authorization: Bearer <token>
//
// Reponse :
//   200 OK → { "tasks": [ { id, userId, titre, completed }, ... ] }
// =======================================================================
app.get('/tasks', verifyToken, async (req, res) => {
  //              ↑ verifyToken s execute en premier avant la fonction
  try {
    const tasks = await getTasks();

    // Filtrer les taches de CET utilisateur uniquement
    // req.userId a ete defini par verifyToken
    // Chaque utilisateur ne voit QUE ses propres taches
    const userTasks = tasks.filter(t => t.userId === req.userId);

    res.json({ tasks: userTasks });

  } catch (error) {
    res.status(500).json({ error: 'Erreur serveur' });
  }
});

// =======================================================================
// ROUTE 2 : POST /tasks — CREER une tache
// =======================================================================
// Headers requis : Authorization: Bearer <token>
// Corps attendu  : { "titre": "Ma nouvelle tache" }
//
// Reponse :
//   201 Created → { "message": "Tache creee", "task": { ... } }
// =======================================================================
app.post('/tasks', verifyToken, async (req, res) => {
  try {
    const { titre } = req.body;

    if (!titre) {
      return res.status(400).json({ error: 'Titre requis' });
    }

    const tasks = await getTasks();

    const newTask = {
      id: tasks.length + 1,  // ID auto-incremente
      userId: req.userId,    // Lie la tache a l utilisateur connecte
      titre,
      completed: false       // Nouvelle tache = non terminee par defaut
    };

    tasks.push(newTask);
    await saveTasks(tasks);

    res.status(201).json({ message: 'Tache creee', task: newTask });

  } catch (error) {
    res.status(500).json({ error: 'Erreur serveur' });
  }
});

// =======================================================================
// ROUTE 3 : PATCH /tasks/:id — MODIFIER une tache
// =======================================================================
// Inverse l etat completed : false → true ou true → false.
// Securite : l utilisateur ne peut modifier QUE ses propres taches.
//
// Headers requis : Authorization: Bearer <token>
// URL exemple    : PATCH /tasks/3
//
// Reponse :
//   200 OK  → { "message": "Tache mise a jour", "task": { ... } }
//   404     → { "error": "Tache non trouvee" }
// =======================================================================
app.patch('/tasks/:id', verifyToken, async (req, res) => {
  try {
    // req.params.id = "3" (chaine) → parseInt() = 3 (nombre)
    const taskId = parseInt(req.params.id);
    const tasks  = await getTasks();

    // Chercher la tache avec DOUBLE condition :
    //   1. t.id === taskId         → l ID correspond
    //   2. t.userId === req.userId → elle appartient a cet utilisateur
    // Si Alice essaie de modifier la tache de Bob → non trouve → 404
    const task = tasks.find(t => t.id === taskId && t.userId === req.userId);

    if (!task) {
      return res.status(404).json({ error: 'Tache non trouvee' });
    }

    // Inverser l etat : false → true, true → false
    task.completed = !task.completed;

    await saveTasks(tasks);
    res.json({ message: 'Tache mise a jour', task });

  } catch (error) {
    res.status(500).json({ error: 'Erreur serveur' });
  }
});

// =======================================================================
// ROUTE 4 : DELETE /tasks/:id — SUPPRIMER une tache
// =======================================================================
// Securite : l utilisateur ne peut supprimer QUE ses propres taches.
//
// Headers requis : Authorization: Bearer <token>
// URL exemple    : DELETE /tasks/3
//
// Reponse :
//   200 OK → { "message": "Tache supprimee" }
//   404    → { "error": "Tache non trouvee" }
// =======================================================================
app.delete('/tasks/:id', verifyToken, async (req, res) => {
  try {
    const taskId = parseInt(req.params.id);
    let tasks    = await getTasks();

    // findIndex() retourne la POSITION dans le tableau (ou -1 si non trouve)
    // On a besoin de la position pour utiliser splice()
    const index = tasks.findIndex(t => t.id === taskId && t.userId === req.userId);

    if (index === -1) {
      return res.status(404).json({ error: 'Tache non trouvee' });
    }

    // splice(position, nombreASupprimer) : supprime des elements du tableau
    // splice(1, 1) sur [a, b, c] → [a, c]
    tasks.splice(index, 1);

    await saveTasks(tasks);
    res.json({ message: 'Tache supprimee' });

  } catch (error) {
    res.status(500).json({ error: 'Erreur serveur' });
  }
});

// ---- DEMARRER LE SERVICE ----
const PORT = 3002;
app.listen(PORT, () => {
  console.log(`\n Tasks Service   → http://localhost:${PORT}`);
  console.log(`   GET    /tasks         (lire les taches)`);
  console.log(`   POST   /tasks         (creer une tache)`);
  console.log(`   PATCH  /tasks/:id     (modifier une tache)`);
  console.log(`   DELETE /tasks/:id     (supprimer une tache)\n`);
});
```

---

## 10. INTERFACE WEB — public/index.html

### Role

Fichier HTML servi par `api.js` sur le port 3000.
Permet de tester l application visuellement sans utiliser PowerShell.

### Ce que fait l interface

```
Onglet 1 : AUTHENTIFICATION
  ┌─────────────────────────────┐
  │  Email    : [____________]  │
  │  Password : [____________]  │
  │                             │
  │  [SE CONNECTER]             │ → POST http://localhost:3001/auth/login
  │  [CREER UN COMPTE]          │ → POST http://localhost:3001/auth/register
  │                             │
  │  Compte de test :           │
  │  alice@email.com / password123 │
  └─────────────────────────────┘

Onglet 2 : MES TACHES (visible apres connexion)
  ┌─────────────────────────────┐
  │  Token JWT : eyJhbGci...    │ ← Token recu du serveur
  │                             │
  │  Nouvelle tache : [______]  │
  │  [AJOUTER LA TACHE]         │ → POST http://localhost:3002/tasks
  │                             │
  │  ┌───────────────────────┐  │
  │  │ Apprendre JWT         │  │ → PATCH  /tasks/1  (terminer)
  │  │ [TERMINER] [SUPPRIMER]│  │ → DELETE /tasks/1  (supprimer)
  │  └───────────────────────┘  │
  │                             │
  │  [SE DECONNECTER]           │
  └─────────────────────────────┘
```

### Comment l interface appelle les microservices

```javascript
// Dans index.html (JavaScript cote client)

// Les deux services ont des URL DIFFERENTES
const AUTH_URL  = 'http://localhost:3001';  // Auth Service
const TASKS_URL = 'http://localhost:3002';  // Tasks Service

// Connexion → appel vers Auth Service
fetch(`${AUTH_URL}/auth/login`, {
  method: 'POST',
  headers: { 'Content-Type': 'application/json' },
  body: JSON.stringify({ email, password })
});

// Lire les taches → appel vers Tasks Service avec le TOKEN
fetch(`${TASKS_URL}/tasks`, {
  headers: { 'Authorization': `Bearer ${token}` }
});
```

---

## 11. ARCHITECTURE MICROSERVICES EXPLIQUEE

### Definition

Les microservices = decoupe l application en plusieurs petits services
independants, chacun avec sa propre responsabilite et son propre port.

### Comparaison detaillee

```
MONOLITHIQUE (un seul api.js)      MICROSERVICES (notre projet)
──────────────────────────────     ────────────────────────────────────
Un seul serveur                    Plusieurs serveurs independants
Un seul port                       Un port par service
Tout meurt si une partie plante    Chaque service peut survivre seul
Tout se deploie ensemble           Chaque service se deploie seul
Un seul npm start                  npm run start:all (3 serveurs)
```

### Communication entre services

Dans notre projet, les services ne se parlent PAS entre eux.
C est l interface web (navigateur) qui appelle chaque service directement.

```
Navigateur                Auth Service         Tasks Service
    │                         │                     │
    │── POST /auth/login ────► │                     │
    │◄── { token } ───────────│                     │
    │                         │                     │
    │── GET /tasks ───────────────────────────────► │
    │   (avec le token dans   │                     │
    │    le header)           │                     │
    │◄── { tasks: [...] } ──────────────────────────│
```

### La cle qui relie les deux services

Les deux services sont independants MAIS ils partagent la meme `SECRET_KEY`.
C est ce qui permet a Tasks Service de valider les tokens crees par Auth Service.

```
AUTH SERVICE                          TASKS SERVICE
─────────────────────────────────     ──────────────────────────────────
jwt.sign({ userId: 1 },               jwt.verify(token,
  process.env.SECRET_KEY,   ←──────→    process.env.SECRET_KEY)
  { expiresIn: '24h' })
  → cree le token                       → verifie le token

Les deux utilisent LA MEME SECRET_KEY → communication securisee
sans que les services se parlent directement
```

### Independance des services

```
Scenario 1 : Auth Service s arrete
─────────────────────────────────
Auth Service ← ARRETE
Tasks Service ← TOURNE ENCORE

Utilisateurs deja connectes (avec un token valide) ?
→ Peuvent encore utiliser Tasks Service normalement
→ Leur token est valide 24h

Nouveaux utilisateurs ?
→ Ne peuvent pas se connecter (Auth Service est down)
→ Mais les autres continuent

Conclusion : les services sont independants !
```

---

## 12. JWT EXPLIQUE DE A A Z

### Definition

JWT = JSON Web Token = Un "badge numerique" signe et portable.

```
AVANT JWT (sessions) :                AVEC JWT :
──────────────────────────────────    ──────────────────────────
1. Client se connecte                 1. Client se connecte
2. Serveur cree une session           2. Serveur cree un TOKEN signe
3. Serveur MEMORISE la session        3. Serveur envoie le token au client
4. Client envoie l ID de session      4. Client PORTE son token
5. Serveur cherche la session         5. Serveur VERIFIE juste la signature
   → Probleme : si le serveur           → Pas de memoire serveur necessaire
     redémarre, sessions perdues         → Fonctionne avec plusieurs serveurs
```

### Cycle de vie complet

```
ETAPE 1 : CONNEXION
──────────────────
Client ─── POST /auth/login ──────────────────────► Auth Service
           { email, password }
                                                          │
                                                   Verifie bcrypt
                                                          │
                                                   jwt.sign({ userId: 1 },
                                                     SECRET_KEY, { expiresIn: '24h' })
                                                          │
Client ◄── { token: "eyJhbG...", userId: 1 } ────────────┘

ETAPE 2 : UTILISER LE TOKEN
───────────────────────────
Client ─── GET /tasks ────────────────────────────► Tasks Service
           Header: Authorization: Bearer eyJhbG...
                                                          │
                                               verifyToken() middleware
                                               jwt.verify(token, SECRET_KEY)
                                               → decoded = { userId: 1, ... }
                                               → req.userId = 1
                                                          │
                                               Filtre les taches de userId=1
                                                          │
Client ◄── { tasks: [ ... taches de userId 1 ... ] } ────┘
```

### Structure d un token JWT

Un token ressemble a cela :

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VySWQiOjF9.abc123xyz
```

Il est compose de 3 parties separees par des `.` :

```
PARTIE 1       PARTIE 2       PARTIE 3
(HEADER)       (PAYLOAD)      (SIGNATURE)
    │               │               │
    ▼               ▼               ▼
Base64Url       Base64Url       HMACSHA256(
encode de :     encode de :       header + payload,
                                  SECRET_KEY
{ "alg": "HS256",  { "userId": 1,  )
  "typ": "JWT" }     "iat": 123...,
                     "exp": 456... }
```

Decoder la partie PAYLOAD (exemple avec jwt.io) :

```json
{
  "userId": 1,
  "iat": 1745678900,
  "exp": 1745765300
}
```

```
userId : l ID de l utilisateur qu on a mis dans jwt.sign()
iat    : "Issued At"  = timestamp Unix de creation
exp    : "Expiration" = timestamp Unix d expiration
```

### Securite du JWT

```
LE PAYLOAD N EST PAS CHIFFRE :
→ Tout le monde peut decoder la partie PAYLOAD (c est du Base64)
→ MAIS impossible de modifier le payload sans invalider la signature

TENTATIVE DE PIRATAGE :
1. Pirate intercepte le token d Alice
2. Il decode le payload : { "userId": 1 }
3. Il modifie : { "userId": 999 }
4. Il rencode en Base64
5. Il renvoie le token modifie

RESULTAT :
jwt.verify() recalcule la signature avec le NOUVEAU payload
La nouvelle signature ≠ la signature originale
→ Le token est REJETE → Erreur 403 Forbidden

CONCLUSION :
Le payload est LISIBLE mais pas MODIFIABLE sans la SECRET_KEY
```

### Code de reference

```javascript
// CREER un token (dans Auth Service)
const token = jwt.sign(
  { userId: user.id },       // Payload
  process.env.SECRET_KEY,    // Cle secrete
  { expiresIn: '24h' }       // Options
);

// VERIFIER un token (dans Tasks Service)
try {
  const decoded = jwt.verify(token, process.env.SECRET_KEY);
  // decoded = { userId: 1, iat: ..., exp: ... }
  console.log(decoded.userId); // 1
} catch (error) {
  // Token invalide ou expire
  console.log('Acces refuse');
}

// ENVOYER le token dans une requete (cote client)
fetch('/tasks', {
  headers: {
    'Authorization': `Bearer ${token}`
  }
});

// RECUPERER le token depuis le header (cote serveur)
const token = req.headers.authorization?.split(' ')[1];
// "Bearer eyJhbGci..."  →  ["Bearer", "eyJhbGci..."]  →  [1]  =  "eyJhbGci..."
```

---

## 13. bcrypt EXPLIQUE DE A A Z

### Le probleme sans bcrypt

```json
// CE QU ON NE DOIT JAMAIS FAIRE : mots de passe en clair
{
  "email": "alice@email.com",
  "password": "password123"
}
```

Si un pirate accede a la base de donnees → il voit tous les mots de passe.

### La solution : hachage avec bcrypt

```
"password123"
      │
      ▼
bcrypt.hash("password123", 10)
      │
      ▼
"$2a$10$N9qo8uLOickgx2ZMRZoMyeQi8Ke/r6yEqK8Ix6vUx4FGYmvnFwsW"
      │
      ▼
C est ce hash qui est stocke en base.
Meme si quelqu un vole la base → il ne peut pas retrouver "password123"
```

### Les 3 proprietes essentielles de bcrypt

```
1. IRREVERSIBLE
   "password123" → hash    ✓ (hacher)
   hash → "password123"    ✗ (impossible de retrouver)

2. AVEC SALT (unicite)
   bcrypt.hash("password123", 10) → "$2a$10$abc..."
   bcrypt.hash("password123", 10) → "$2a$10$xyz..."  ← different !
   Meme mot de passe = hash different a chaque fois
   (Le salt est genere aleatoirement et inclus dans le hash)

3. LENT PAR CONCEPTION (protection brute force)
   1 hash ≈ 100 millisecondes
   Tester 1 million de mots de passe = 27 heures
   → Rend les attaques "force brute" impossibles en pratique
```

### Code de reference

```javascript
// INSCRIPTION : hasher le mot de passe avant de le stocker
const saltRounds = 10; // Nombre de tours (plus eleve = plus lent et securise)
const hash = await bcrypt.hash("password123", saltRounds);
// Stocker 'hash' dans la base, jamais "password123"

// CONNEXION : comparer le mot de passe saisi avec le hash stocke
const estValide = await bcrypt.compare("password123", hash);
// true  → mot de passe correct
// false → mot de passe incorrect
```

### Ce qui est stocke dans users.json

```json
{
  "id": 1,
  "email": "alice@email.com",
  "password": "$2a$10$N9qo8uLOickgx2ZMRZoMyeQi8Ke/r6yEqK8Ix6vUx4FGYmvnFwsW"
}
```

---

## 14. LES MIDDLEWARES EXPLIQUES

### Definition

Un middleware = une fonction qui s execute ENTRE la reception de la requete
et l envoi de la reponse.

```
Requete → [Middleware 1] → [Middleware 2] → [Route] → Reponse
```

### Signature d un middleware

```javascript
// Tout middleware a la meme forme : 3 parametres
function monMiddleware(req, res, next) {
  //
  // ... traitement ...
  //
  next(); // OBLIGATOIRE : passe au middleware/route suivant(e)
          // Si next() n est pas appele → la requete est bloquee !
}
```

### Les middlewares dans ce projet

```
Requete entrante
      │
      ▼
┌─────────────────────────────────────┐
│  Middleware 1 : express.json()      │  Lit le JSON du body → req.body
└──────────────────┬──────────────────┘
                   │
                   ▼
┌─────────────────────────────────────┐
│  Middleware 2 : CORS                │  Autorise les requetes cross-origin
└──────────────────┬──────────────────┘
                   │
                   ▼
┌─────────────────────────────────────┐
│  Middleware 3 : verifyToken()       │  Verifie le token JWT (routes protegees)
│  (uniquement sur /tasks)            │  → ajoute req.userId
└──────────────────┬──────────────────┘
                   │
                   ▼
           [Route handler]
           Code metier de la route
```

### Utiliser un middleware sur une seule route

```javascript
// Syntaxe : app.methode(url, middleware, handler)
app.get('/tasks', verifyToken, async (req, res) => {
  //              ↑
  //   verifyToken s execute en premier
  //   Si token invalide → stoppe ici
  //   Si token valide   → passe a la fonction suivante
  
  const tasks = await getTasks();
  res.json({ tasks });
});
```

---

## 15. CORS EXPLIQUE

### Le probleme

Le navigateur bloque par securite les requetes vers un port (ou domaine) different.

```
Interface web sur port 3000 appelle Auth Service sur port 3001

SANS CORS :
Navigateur → "Je vais appeler http://localhost:3001/auth/login"
Navigateur → BLOQUE ! La reponse vient d un port different → erreur CORS

AVEC CORS (header Access-Control-Allow-Origin: *) :
Navigateur → "Je vais appeler http://localhost:3001/auth/login"
Serveur    → "Je t autorise (Access-Control-Allow-Origin: *)"
Navigateur → OK, je passe la reponse au JavaScript
```

### Code CORS dans nos services

```javascript
app.use((req, res, next) => {
  // * = toute origine autorisee (en production, specifier le domaine exact)
  res.header('Access-Control-Allow-Origin', '*');
  
  // Autoriser ces en-tetes dans les requetes du client
  res.header('Access-Control-Allow-Headers', 'Content-Type, Authorization');
  
  // Autoriser ces methodes HTTP
  res.header('Access-Control-Allow-Methods', 'GET, POST, PATCH, DELETE');
  
  // Passer au middleware suivant
  next();
});
```

---

## 16. TESTER L APPLICATION

### Demarrage

```powershell
# Installer les dependances (une seule fois)
npm install

# Lancer les 3 serveurs
npm run start:all
```

### Test via l interface web (recommande pour debutants)

1. Ouvrir le navigateur : http://localhost:3000
2. Se connecter avec `alice@email.com` / `password123`
3. Observer le token JWT qui s affiche
4. Creer, modifier, supprimer des taches

### Tests via PowerShell (recommande pour comprendre les requetes HTTP)

#### S inscrire

```powershell
$body = @{
  email    = 'etudiant@email.com'
  password = 'monmotdepasse'
} | ConvertTo-Json

Invoke-RestMethod `
  -Method POST `
  -Uri http://localhost:3001/auth/register `
  -Body $body `
  -ContentType 'application/json'
```

Reponse attendue :

```json
{ "message": "Utilisateur cree", "id": 2 }
```

#### Se connecter et recuperer le token

```powershell
$body = @{
  email    = 'alice@email.com'
  password = 'password123'
} | ConvertTo-Json

$response = Invoke-RestMethod `
  -Method POST `
  -Uri http://localhost:3001/auth/login `
  -Body $body `
  -ContentType 'application/json'

# Stocker le token dans une variable
$token = $response.token
Write-Host "Token JWT : $token"
```

Reponse attendue :

```json
{
  "message": "Connexion reussie",
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "userId": 1
}
```

#### Voir ses taches (avec le token)

```powershell
$headers = @{ Authorization = "Bearer $token" }

Invoke-RestMethod `
  -Uri http://localhost:3002/tasks `
  -Headers $headers
```

#### Creer une tache

```powershell
$headers = @{ Authorization = "Bearer $token" }
$body = @{ titre = 'Faire les courses' } | ConvertTo-Json

Invoke-RestMethod `
  -Method POST `
  -Uri http://localhost:3002/tasks `
  -Body $body `
  -ContentType 'application/json' `
  -Headers $headers
```

#### Modifier une tache (toggle completed)

```powershell
$headers = @{ Authorization = "Bearer $token" }

Invoke-RestMethod `
  -Method PATCH `
  -Uri http://localhost:3002/tasks/1 `
  -Headers $headers
```

#### Supprimer une tache

```powershell
$headers = @{ Authorization = "Bearer $token" }

Invoke-RestMethod `
  -Method DELETE `
  -Uri http://localhost:3002/tasks/1 `
  -Headers $headers
```

#### Tester sans token (doit echouer)

```powershell
Invoke-RestMethod -Uri http://localhost:3002/tasks
# Reponse : { "error": "Token manquant" }  Code : 401
```

#### Tester avec un faux token (doit echouer)

```powershell
$headers = @{ Authorization = "Bearer tokenfalsifie" }
Invoke-RestMethod -Uri http://localhost:3002/tasks -Headers $headers
# Reponse : { "error": "Token invalide" }  Code : 403
```

---

## 17. GLOSSAIRE COMPLET

| Terme | Definition |
|-------|-----------|
| **API** | Application Programming Interface : interface de communication entre programmes |
| **REST** | Representational State Transfer : style d architecture pour les API HTTP |
| **Microservice** | Petit service independant avec une responsabilite unique |
| **Monolithique** | Application ou tout le code est dans un seul programme |
| **Express** | Framework web pour Node.js qui simplifie la creation de serveurs HTTP |
| **Node.js** | Environnement qui permet d executer JavaScript hors navigateur |
| **npm** | Node Package Manager : gestionnaire de paquets de Node.js |
| **package.json** | Fichier de configuration du projet (dependances, scripts, metadata) |
| **node_modules** | Dossier contenant les packages installes par npm |
| **Route** | Combinaison methode HTTP + URL + fonction de traitement |
| **Endpoint** | URL specifique d une API (ex : /tasks, /auth/login) |
| **Middleware** | Fonction executee entre la reception et la reponse |
| **req** | Objet contenant les informations de la requete (body, headers, params...) |
| **res** | Objet permettant d envoyer la reponse au client |
| **next()** | Fonction qui passe au middleware ou route suivant(e) |
| **CRUD** | Create Read Update Delete : les 4 operations de base |
| **JWT** | JSON Web Token : token signe qui prouve l identite |
| **Payload** | Donnees contenues dans un JWT (non chiffrees mais signees) |
| **Signature** | Partie du JWT garantissant qu il n a pas ete modifie |
| **SECRET_KEY** | Cle secrete utilisee pour signer et verifier les tokens JWT |
| **Bearer** | Prefixe du token dans le header Authorization |
| **bcrypt** | Algorithme de hachage concu pour les mots de passe |
| **Hash** | Resultat du hachage (chaine illisible et irreversible) |
| **Salt** | Valeur aleatoire ajoutee au mot de passe avant hachage (unicite) |
| **Salt rounds** | Nombre de tours de chiffrement bcrypt (10 = standard) |
| **CORS** | Cross-Origin Resource Sharing : mecanisme d autorisation cross-domaine |
| **.env** | Fichier stockant les variables d environnement secretes |
| **process.env** | Objet Node.js permettant d acceder aux variables d environnement |
| **async/await** | Syntaxe JavaScript pour gerer les operations asynchrones |
| **JSON** | JavaScript Object Notation : format d echange de donnees |
| **Status Code** | Code numerique HTTP indiquant le resultat (200, 201, 400, 401, 403, 404, 500) |
| **Authorization header** | En-tete HTTP transportant le token d authentification |
| **concurrently** | Package npm permettant de lancer plusieurs commandes en parallele |
| **nodemon** | Outil de developpement qui redémarre automatiquement le serveur |

---

## 18. QUESTIONS DE VALIDATION

### Niveau 1 : Comprehension de base

1. Quelle est la difference entre une application monolithique et une architecture microservices ?
2. Sur quel port tourne chaque service de ce projet ?
3. Pourquoi ne stocke-t-on pas les mots de passe en clair dans la base de donnees ?
4. Qu est-ce qu un middleware ? Donnez un exemple dans ce projet.
5. Que signifie CRUD ? Donnez la methode HTTP correspondant a chaque operation.

### Niveau 2 : Comprehension approfondie

6. Pourquoi le fichier `.env` ne doit-il jamais etre envoye sur Git ?
7. Qu est-ce que le CORS ? Pourquoi est-il necessaire dans ce projet ?
8. Que se passe-t-il si on supprime le middleware `express.json()` ?
9. Pourquoi `jwt.verify()` peut-il rejeter un token meme si la signature est valide ?
10. Pourquoi le payload du JWT n est-il pas chiffre ? Quelles sont les consequences ?

### Niveau 3 : Analyse du code

11. Dans `verifyToken()`, expliquez ce que fait `req.headers.authorization?.split(' ')[1]`
12. Dans `GET /tasks`, pourquoi utilise-t-on `filter()` au lieu de retourner toutes les taches ?
13. Dans `POST /auth/login`, pourquoi dit-on "email OU mot de passe incorrect" au lieu de "email inconnu" ?
14. Expliquez la double condition dans `tasks.find(t => t.id === taskId && t.userId === req.userId)`
15. Comment Tasks Service peut-il valider les tokens crees par Auth Service sans les contacter ?

### Reponses rapides (pour auto-correction)

```
1.  Monolithique = 1 serveur tout-en-un / Microservices = plusieurs services independants
2.  api.js=3000, auth-service=3001, tasks-service=3002
3.  Si la base est volee, les mots de passe ne sont pas exposes
4.  Fonction entre requete et reponse. Ex : verifyToken(), express.json()
5.  Create=POST, Read=GET, Update=PATCH, Delete=DELETE
6.  Contient des secrets (SECRET_KEY) qui ne doivent pas etre publics
7.  Autoriser les requetes entre ports/domaines differents
8.  req.body serait undefined dans toutes les routes POST/PATCH
9.  Si le token est expire (champ exp dans le payload)
10. Quiconque decode le token peut lire le payload → ne jamais y mettre de secrets
11. Lit le header, divise par espace, prend le 2eme element (le token sans "Bearer")
12. Pour l isolation des donnees : chaque user ne voit que ses propres taches
13. Pour ne pas reveler si l email existe dans la base (securite)
14. Verifie l ID ET que la tache appartient bien a cet utilisateur (securite)
15. Les deux utilisent la meme SECRET_KEY pour signer/verifier → meme resultat
```

---

## RESUME FINAL

```
┌─────────────────────────────────────────────────────────────────┐
│                    SCHEMA GLOBAL DU PROJET                      │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   NAVIGATEUR ──► http://localhost:3000 (api.js)                 │
│        │         └── Sert public/index.html                     │
│        │                                                        │
│        ├──► POST /auth/register ──► Auth Service (3001)         │
│        │                            ├── Hashe mdp (bcrypt)      │
│        │                            └── Sauvegarde users.json   │
│        │                                                        │
│        ├──► POST /auth/login    ──► Auth Service (3001)         │
│        │        │                   ├── Verifie bcrypt           │
│        │        │                   └── Cree token JWT           │
│        │        │                                                │
│        │    ◄── token JWT                                       │
│        │                                                        │
│        ├──► GET /tasks          ──► Tasks Service (3002)        │
│        │    + header: Bearer token  ├── verifyToken() middleware │
│        │                            ├── Decode JWT → userId      │
│        │                            └── Filtre tasks par userId  │
│        │                                                        │
│        ├──► POST /tasks         ──► Tasks Service (3002)        │
│        ├──► PATCH /tasks/:id    ──► Tasks Service (3002)        │
│        └──► DELETE /tasks/:id   ──► Tasks Service (3002)        │
│                                                                 │
│   DONNEES PARTAGEES :                                           │
│   data/users.json ← Auth Service ecrit                         │
│   data/tasks.json ← Tasks Service lit et ecrit                 │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

*Documentation creee pour le cours API REST avec Architecture Microservices*
*Compte de test : alice@email.com / password123*
