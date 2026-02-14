# 🎓 API REST avec JWT - Cours pour Débutants

## 📚 Table des matières
1. [Qu'est-ce qu'une API REST ?](#1-quest-ce-quune-api-rest)
2. [Qu'est-ce que JWT ?](#2-quest-ce-que-jwt)
3. [Qu'est-ce que bcrypt ?](#3-quest-ce-que-bcrypt)
4. [Qu'est-ce qu'un middleware ?](#4-quest-ce-quun-middleware)
5. [Exercice pratique](#5-exercice-pratique)

---

## 1. Qu'est-ce qu'une API REST ?

### 📖 Définition
**API REST** = Un serveur qui permet à des applications de communiquer entre elles via HTTP.

**Analogie simple** : C'est comme un restaurant 🍽️
- Le **client** (votre application) passe une commande
- Le **serveur** (API) prend la commande
- La **cuisine** (base de données) prépare
- Le **serveur** vous rapporte le plat

### Les 4 opérations de base (CRUD)

| Méthode HTTP | Action    | Exemple            | Résultat                     |
|--------------|-----------|--------------------|------------------------------|
| **GET**      | Lire      | `GET /tasks`       | Récupère la liste des tâches |
| **POST**     | Créer     | `POST /tasks`      | Crée une nouvelle tâche      |
| **PATCH**    | Modifier  | `PATCH /tasks/1`   | Modifie la tâche n°1         |
| **DELETE**   | Supprimer | `DELETE /tasks/1`  | Supprime la tâche n°1        |

### 📝 Vocabulaire important

- **Endpoint** : Une URL de l'API (exemple : `/tasks`)
- **Route** : Un endpoint + une méthode HTTP (exemple : `GET /tasks`)
- **Body** : Les données envoyées avec POST/PATCH
- **Headers** : Informations supplémentaires (exemple : `Authorization`)
- **Status Code** : Code de réponse (200 = OK, 404 = Non trouvé, 500 = Erreur)

---

## 2. Qu'est-ce que JWT ?

### 📖 Définition
**JWT** (JSON Web Token) = Un "badge numérique" qui prouve votre identité sans avoir à vous reconnecter à chaque fois.

### Comment ça fonctionne ?

```
Étape 1 : Login
Client → [email + password] → Serveur
                                 ↓
                            Vérifie l'identité
                                 ↓
Serveur → [Token JWT] → Client

Étape 2 : Requêtes suivantes
Client → [Token dans le Header] → Serveur
                                     ↓
                                Vérifie le token
                                     ↓
                               Autorise l'accès
```

### Structure d'un JWT

Un JWT ressemble à ça :
```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VySWQiOjF9.abc123xyz
```

Il est composé de **3 parties** séparées par des points (`.`) :

```
HEADER . PAYLOAD . SIGNATURE
```

#### 1️⃣ HEADER (En-tête)
```json
{
  "alg": "HS256",    ← Algorithme utilisé
  "typ": "JWT"       ← Type : JWT
}
```

#### 2️⃣ PAYLOAD (Données)
```json
{
  "userId": 1,           ← ID de l'utilisateur
  "iat": 1645123456,     ← Date de création
  "exp": 1645209856      ← Date d'expiration
}
```

#### 3️⃣ SIGNATURE (Preuve de validité)
```
HMACSHA256(
  header + payload,
  SECRET_KEY          ← Clé secrète connue seulement du serveur
)
```

### 🔒 Pourquoi c'est sécurisé ?

**La signature cryptographique !**

```
✅ Cas normal :
Token créé par le serveur avec SA clé secrète
→ Signature valide
→ Accès autorisé

❌ Tentative de piratage :
Quelqu'un modifie le userId dans le token
→ Signature invalide (ne correspond plus)
→ jwt.verify() détecte la modification
→ Accès refusé
```

### 📝 Vocabulaire JWT

- **Token** : La chaîne complète (header + payload + signature)
- **Clé secrète** : Mot de passe pour signer le token (dans le fichier `.env`)
- **Bearer** : Type d'authentification (`Authorization: Bearer <token>`)
- **Expiration** : Durée de validité du token (exemple : 24h)
- **Payload** : Les données contenues dans le token

---

## 3. Qu'est-ce que bcrypt ?

**Hash :** Fonction cryptographique à sens unique qui transforme une donnée en une empreinte numérique fixe, non réversible, utilisée pour vérifier l’intégrité ou sécuriser des mots de passe.

**Cryptage (Chiffrement) :** Procédé réversible qui transforme une donnée lisible en donnée illisible à l’aide d’une clé afin de protéger sa confidentialité.

### 📖 Définition
**bcrypt** = Une fonction qui transforme un mot de passe en une chaîne illisible (hash) pour le sécuriser.

### ❌ Le problème

Si on stocke les mots de passe en clair :
```json
{
  "email": "alice@email.com",
  "password": "monmotdepasse123"  ← ❌ DANGER !
}
```

**Risque** : Si quelqu'un accède à la base de données, il voit tous les mots de passe !

### ✅ La solution : bcrypt

```
Mot de passe : "monmotdepasse123"
       ↓
bcrypt.hash(password, 10)
       ↓
Hash : "$2a$10$abc123..." ← Impossible à décoder !
```

### Comment l'utiliser ?

#### Lors de l'inscription
```javascript
const password = "monmotdepasse123";
const hash = await bcrypt.hash(password, 10);
// Résultat : "$2a$10$abc123..."
// On stocke CE HASH dans la base
```

#### Lors de la connexion
```javascript
const password = "monmotdepasse123";  // Entré par l'utilisateur
const hash = "$2a$10$abc123...";      // Récupéré de la base

const valid = await bcrypt.compare(password, hash);
// Résultat : true ou false
```

### 🔒 Pourquoi c'est sécurisé ?

1. **Unidirectionnel** : Impossible de retrouver le mot de passe à partir du hash
2. **Salt automatique** : Même mot de passe = hash différent à chaque fois
3. **Lent par design** : Rend les attaques par force brute très difficiles

### 📝 Vocabulaire bcrypt

- **Hash** : Version cryptée du mot de passe
- **Salt** : Valeur aléatoire ajoutée pour rendre le hash unique
- **Rounds** : Nombre de tours de chiffrement (10 = recommandé)

---

## 4. Qu'est-ce qu'un middleware ?

### 📖 Définition
**Middleware** = Une fonction qui s'exécute **entre** la réception de la requête et l'exécution de la route.

### Analogie simple

Imaginez un contrôle de sécurité à l'entrée d'un bâtiment 🏢

```
Client → [Porte d'entrée] → Middleware (contrôle du badge) → Route (bureau)
                                   ↓
                            Badge valide ?
                              /        \
                            OUI        NON
                             ↓          ↓
                        Accès OK    Accès refusé
```

### Exemple concret

```javascript
function verifyToken(req, res, next) {
  // 1. Récupérer le token
  const token = req.headers.authorization?.split(' ')[1];
  
  if (!token) {
    return res.status(401).json({ error: 'Token manquant' });
  }
  
  // 2. Vérifier le token
  try {
    const decoded = jwt.verify(token, process.env.SECRET_KEY);
    req.userId = decoded.userId; // Stocker l'userId
    next(); // Passer à la route
  } catch (error) {
    return res.status(403).json({ error: 'Token invalide' });
  }
}

// Utilisation
router.get('/tasks', verifyToken, async (req, res) => {
  //                   ↑ Middleware exécuté en premier
  const userTasks = tasks.filter(t => t.userId === req.userId);
  res.json({ tasks: userTasks });
});
```

### 📝 Vocabulaire middleware

- **next()** : Fonction qui passe à la prochaine étape
- **req** : Objet contenant les données de la requête
- **res** : Objet pour envoyer la réponse

---

## 5. Exercice pratique

### 🏗️ Structure du projet

```
apirestexercice/
│
├── api.js                    ← Serveur principal (SIMPLE)
│
├── routes/                   ← Routes séparées
│   ├── authRoutes.js         ← Routes d'authentification
│   └── taskRoutes.js         ← Routes des tâches
│
├── data/                     ← Base de données JSON
│   ├── users.json            ← Utilisateurs
│   └── tasks.json            ← Tâches
│
├── public/                   ← Interface web
│   └── index.html            ← Page de démonstration visuelle
│
├── .env                      ← Clé secrète JWT
└── package.json              ← Dépendances
```

### 🚀 Installation et démarrage

```powershell
# Installer les dépendances
npm install

# Démarrer le serveur
npm start
```

Le serveur démarre sur **http://localhost:3000**

---

## 🌐 TESTER AVEC L'INTERFACE WEB (Recommandé pour débuter)

### Option 1 : Interface Visuelle (Plus facile pour comprendre)

1. **Démarrez le serveur** : `npm start`
2. **Ouvrez votre navigateur** : http://localhost:3000
3. **Vous verrez une interface graphique** avec deux onglets :
   - 🔐 **Authentification** : S'inscrire et se connecter
   - ✅ **Mes Tâches** : Gérer vos tâches

#### 🎯 Étapes pour tester :

1. **Testez le compte par défaut** :
   - Email : `alice@email.com`
   - Mot de passe : `password123`
   - Cliquez sur "Se connecter"

2. **Observez le token JWT** :
   - Après connexion, vous verrez votre token JWT affiché
   - Ce token prouve votre identité pour 24h

3. **Créez une tâche** :
   - Entrez un titre (ex: "Faire les courses")
   - Cliquez sur "➕ Ajouter la tâche"
   - La tâche apparaît instantanément !

4. **Modifiez une tâche** :
   - Cliquez sur "✅ Terminer" pour marquer comme terminée
   - Cliquez sur "↩️ Annuler" pour la remettre en cours

5. **Supprimez une tâche** :
   - Cliquez sur "🗑️" pour supprimer

6. **Créez un nouveau compte** :
   - Déconnectez-vous
   - Entrez un nouvel email
   - Cliquez sur "Créer un compte"
   - Connectez-vous avec ce nouveau compte
   - Vos tâches sont isolées ! Vous ne voyez que VOS tâches.

### 💡 Ce que vous apprenez avec l'interface :

- ✅ Comment fonctionne l'**inscription** et la **connexion**
- ✅ À quoi ressemble un **token JWT**
- ✅ Comment le token est envoyé dans chaque requête (automatique)
- ✅ Comment les données sont **isolées par utilisateur**
- ✅ Le cycle complet d'une API REST avec JWT

### 🔍 Ce qui se passe en arrière-plan

Quand vous utilisez l'interface web, voici ce qui se passe techniquement :

#### 1️⃣ Lors de la connexion :

```
Vous cliquez sur "Se connecter"
         ↓
JavaScript envoie une requête :
  POST http://localhost:3000/auth/login
  Body: { "email": "alice@email.com", "password": "password123" }
         ↓
Le serveur vérifie avec bcrypt
         ↓
Le serveur crée un token JWT
         ↓
Le serveur renvoie : { "token": "eyJhbGci...", "userId": 1 }
         ↓
JavaScript stocke le token en mémoire
         ↓
Vous voyez le token affiché à l'écran !
```

#### 2️⃣ Lors de la création d'une tâche :

```
Vous cliquez sur "➕ Ajouter la tâche"
         ↓
JavaScript envoie une requête :
  POST http://localhost:3000/tasks
  Headers: { "Authorization": "Bearer eyJhbGci..." }
  Body: { "titre": "Faire les courses" }
         ↓
Le middleware verifyToken() vérifie le token
         ↓
Si valide, le serveur récupère le userId du token
         ↓
Le serveur crée la tâche avec CE userId
         ↓
La tâche apparaît instantanément dans la liste !
```

#### 3️⃣ Isolation des données :

```
Alice se connecte (userId: 1)
  → Ses tâches : ID 1, 2, 3
  
Bob se connecte (userId: 2)
  → Ses tâches : ID 4, 5
  
Chaque utilisateur voit SEULEMENT ses tâches !
(Filtré par userId dans le token JWT)
```

---

## 🧪 TESTER AVEC POWERSHELL (Option avancée)

#### Test 1 : S'inscrire

```powershell
$body = @{
  email = 'bob@email.com'
  password = 'password123'
} | ConvertTo-Json

Invoke-RestMethod -Method POST -Uri http://localhost:3000/auth/register -Body $body -ContentType 'application/json'
```

**Résultat** :
```json
{
  "message": "Utilisateur créé",
  "id": 2
}
```

#### Test 2 : Se connecter (recevoir le token JWT)

```powershell
$body = @{
  email = 'alice@email.com'
  password = 'password123'
} | ConvertTo-Json

$response = Invoke-RestMethod -Method POST -Uri http://localhost:3000/auth/login -Body $body -ContentType 'application/json'
$token = $response.token
Write-Host "Token JWT reçu : $token"
```

**Résultat** :
```json
{
  "message": "Connexion réussie",
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "userId": 1
}
```

#### Test 3 : Voir ses tâches (avec le token)

```powershell
$headers = @{
  Authorization = "Bearer $token"
}

Invoke-RestMethod -Uri http://localhost:3000/tasks -Headers $headers
```

**Résultat** :
```json
{
  "tasks": [
    {
      "id": 1,
      "userId": 1,
      "titre": "Apprendre JWT",
      "completed": false
    }
  ]
}
```

#### Test 4 : Créer une tâche (avec le token)

```powershell
$headers = @{
  Authorization = "Bearer $token"
}

$body = @{
  titre = 'Faire les courses'
} | ConvertTo-Json

Invoke-RestMethod -Method POST -Uri http://localhost:3000/tasks -Body $body -ContentType 'application/json' -Headers $headers
```

**Résultat** :
```json
{
  "message": "Tâche créée",
  "task": {
    "id": 2,
    "userId": 1,
    "titre": "Faire les courses",
    "completed": false
  }
}
```

#### Test 5 : Essayer SANS token (doit échouer)

```powershell
Invoke-RestMethod -Uri http://localhost:3000/tasks
```

**Résultat** :
```json
{
  "error": "Token manquant"
}
```

---

## 📚 Explication du code

### Fichier principal : `api.js`

```javascript
const express = require('express');
const authRoutes = require('./routes/authRoutes');
const taskRoutes = require('./routes/taskRoutes');

const app = express();
app.use(express.json());

// Routes
app.use('/auth', authRoutes);  // Toutes les routes commencent par /auth
app.use('/tasks', taskRoutes); // Toutes les routes commencent par /tasks

app.listen(3000);
```

### Route d'inscription : `routes/authRoutes.js`

```javascript
router.post('/register', async (req, res) => {
  const { email, password } = req.body;
  
  // 1. Hasher le mot de passe avec bcrypt
  const hashedPassword = await bcrypt.hash(password, 10);
  
  // 2. Créer l'utilisateur
  const newUser = {
    id: users.length + 1,
    email,
    password: hashedPassword  // ← Hash, pas le vrai mot de passe
  };
  
  // 3. Sauvegarder dans users.json
  users.push(newUser);
  await saveUsers(users);
});
```

### Route de connexion : `routes/authRoutes.js`

```javascript
router.post('/login', async (req, res) => {
  const { email, password } = req.body;
  
  // 1. Trouver l'utilisateur
  const user = users.find(u => u.email === email);
  
  // 2. Vérifier le mot de passe avec bcrypt
  const validPassword = await bcrypt.compare(password, user.password);
  
  if (!validPassword) {
    return res.status(401).json({ error: 'Mot de passe incorrect' });
  }
  
  // 3. Créer le token JWT
  const token = jwt.sign(
    { userId: user.id },        // Données dans le token
    process.env.SECRET_KEY,     // Clé secrète
    { expiresIn: '24h' }        // Expire dans 24h
  );
  
  // 4. Renvoyer le token au client
  res.json({ token, userId: user.id });
});
```

### Middleware de vérification : `routes/taskRoutes.js`

```javascript
function verifyToken(req, res, next) {
  // 1. Récupérer le token du header Authorization
  const token = req.headers.authorization?.split(' ')[1];
  // Header : "Authorization: Bearer eyJhbGciOi..."
  //                                  └─ On prend cette partie
  
  if (!token) {
    return res.status(401).json({ error: 'Token manquant' });
  }
  
  // 2. Vérifier et décoder le token
  try {
    const decoded = jwt.verify(token, process.env.SECRET_KEY);
    // decoded = { userId: 1, iat: ..., exp: ... }
    
    // 3. Stocker userId dans req
    req.userId = decoded.userId;
    
    // 4. Passer à la route
    next();
  } catch (error) {
    return res.status(403).json({ error: 'Token invalide' });
  }
}
```

### Route protégée : `routes/taskRoutes.js`

```javascript
router.get('/', verifyToken, async (req, res) => {
  //            ↑ Middleware exécuté en premier
  
  // À ce stade, req.userId existe grâce au middleware
  const tasks = await getTasks();
  const userTasks = tasks.filter(t => t.userId === req.userId);
  
  res.json({ tasks: userTasks });
});
```

---

## 🎓 Résumé

### Ce que l'exercice fait :

1. ✅ **Inscription** : Hash le mot de passe avec bcrypt et stocke l'utilisateur
2. ✅ **Connexion** : Vérifie le mot de passe et renvoie un token JWT
3. ✅ **Routes protégées** : Le middleware vérifie le token avant d'accéder aux tâches
4. ✅ **Isolation des données** : Chaque utilisateur voit seulement SES tâches

### 📝 Vocabulaire récapitulatif

| Terme | Définition |
|-------|-----------|
| **API REST** | Serveur qui utilise HTTP pour communiquer |
| **JWT** | Token signé qui prouve l'identité |
| **bcrypt** | Fonction pour hasher les mots de passe |
| **Hash** | Version cryptée d'un mot de passe |
| **Token** | Chaîne contenant des données + signature |
| **Middleware** | Fonction exécutée avant les routes |
| **Route** | Endpoint de l'API (ex: `POST /auth/login`) |
| **Header** | Métadonnées d'une requête HTTP |
| **Body** | Données envoyées avec POST/PATCH |
| **Payload** | Données contenues dans le JWT |

### ❓ Questions pour vérifier votre compréhension

1. **Pourquoi ne pas stocker les mots de passe en clair ?**
   → Réponse : Si la base est piratée, tous les mots de passe sont exposés

2. **À quoi sert la signature dans un JWT ?**
   → Réponse : Elle prouve que le token n'a pas été modifié

3. **Qu'est-ce qu'un middleware ?**
   → Réponse : Une fonction qui s'exécute entre la requête et la route

4. **Que se passe-t-il si on modifie le userId dans le token ?**
   → Réponse : La signature devient invalide et le serveur rejette le token

5. **Comment le serveur sait-il qui fait la requête ?**
   → Réponse : En vérifiant le userId contenu dans le token JWT

### 🎯 Compte de test

- **Email** : alice@email.com
- **Password** : password123

---

## 🎉 Félicitations !

Vous maîtrisez maintenant :
- ✅ Les API REST et leurs opérations CRUD
- ✅ L'authentification JWT
- ✅ Le hachage bcrypt
- ✅ Les middlewares
- ✅ La séparation des routes

**Vous êtes prêt à créer des API sécurisées !** 🚀
