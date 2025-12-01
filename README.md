# GUIDE COMPLET - CREATION D'UNE API REST PAS A PAS

## TABLE DES MATIERES

1. [Cours : Introduction aux API REST](#cours)
2. [TD : Enonce du projet](#td)
3. [Etape 1 : Initialisation du projet](#etape1)
4. [Etape 2 : Creation du serveur de base](#etape2)
5. [Etape 3 : Structure des donnees](#etape3)
6. [Etape 4 : Routes pour les equipes](#etape4)
7. [Etape 5 : Routes pour le market](#etape5)
8. [Etape 6 : Gestion des erreurs](#etape6)
9. [Etape 7 : Interface web](#etape7)
10. [Etape 8 : Tests et validation](#etape8)
11. [Etape 9 : Documentation](#etape9)
12. [Glossaire complet](#glossaire)

---

## COURS : INTRODUCTION AUX API REST {#cours}

### 1. QU'EST-CE QU'UNE API ?

#### Definition

Une API (Application Programming Interface) est une interface qui permet a deux applications de communiquer entre elles. C'est un ensemble de regles et de definitions qui permettent a des logiciels d'echanger des informations.

#### Exemple concret

Lorsque vous utilisez une application mobile de meteo, celle-ci envoie une demande a une API qui lui renvoie les informations meteorologiques. L'application mobile n'a pas besoin de savoir comment les donnees sont stockees ou calculees, elle utilise simplement l'API.

#### Analogie

Imaginez un restaurant :
- Vous (le client) etes l'application qui fait une demande
- Le serveur est l'API qui prend votre commande
- La cuisine est le systeme qui traite votre demande
- Le plat que vous recevez est la reponse de l'API

### 2. HISTORIQUE ET EVOLUTION

#### Les annees 2000 : SOAP

Avant REST, les API utilisaient principalement SOAP (Simple Object Access Protocol). SOAP etait :
- Base sur XML
- Tres structure et rigide
- Complexe a mettre en oeuvre
- Lourd en termes de volume de donnees

#### 2000 : Apparition de REST

Roy Fielding a introduit REST (Representational State Transfer) dans sa these de doctorat en 2000. REST a apporte :
- Une architecture plus simple
- Une meilleure performance
- Une utilisation standard du protocole HTTP
- Une structure plus flexible

#### Aujourd'hui

REST est devenu le standard de facto pour les API web. La plupart des grandes entreprises (Google, Facebook, Twitter, etc.) utilisent des API REST pour leurs services.

### 3. QU'EST-CE QUE REST ?

#### Definition

REST (Representational State Transfer) est un style d'architecture pour concevoir des services web. Une API REST utilise le protocole HTTP pour effectuer des operations sur des ressources.

#### Les 6 principes de REST

**1. Architecture Client-Serveur**
- Separation entre le client (qui demande) et le serveur (qui repond)
- Le client ne se preoccupe pas de comment les donnees sont stockees
- Le serveur ne se preoccupe pas de l'interface utilisateur

**2. Sans etat (Stateless)**
- Chaque requete est independante
- Le serveur ne conserve pas d'information entre deux requetes
- Toutes les informations necessaires sont dans la requete

**3. Cacheable**
- Les reponses peuvent etre mises en cache
- Ameliore les performances
- Reduit la charge du serveur

**4. Interface uniforme**
- Utilisation standard des methodes HTTP
- Structure predictible des URL
- Format de reponse coherent

**5. Systeme en couches**
- L'architecture peut etre composee de plusieurs couches
- Le client ne sait pas s'il communique directement avec le serveur final

**6. Code a la demande (optionnel)**
- Le serveur peut envoyer du code executable au client

### 4. LES METHODES HTTP

REST utilise les methodes HTTP pour definir les actions :

#### GET - Lire des donnees

```
GET /api/equipes
```
- Recupere une liste de ressources
- Ne modifie pas les donnees
- Peut etre mis en cache
- Exemple : Afficher toutes les equipes

```
GET /api/equipes/1
```
- Recupere une ressource specifique
- Exemple : Afficher l'equipe numero 1

#### POST - Creer des donnees

```
POST /api/equipes
```
- Cree une nouvelle ressource
- Envoie des donnees dans le corps de la requete
- Modifie l'etat du serveur
- Exemple : Ajouter une nouvelle equipe

#### PUT - Modifier des donnees

```
PUT /api/equipes/1
```
- Modifie une ressource existante
- Remplace completement la ressource
- Exemple : Modifier l'equipe numero 1

#### DELETE - Supprimer des donnees

```
DELETE /api/equipes/1
```
- Supprime une ressource
- Exemple : Supprimer l'equipe numero 1

### 5. LES CODES DE STATUT HTTP

Les codes de statut indiquent le resultat d'une requete :

#### 2xx - Succes

- **200 OK** : La requete a reussi
- **201 Created** : Une nouvelle ressource a ete creee
- **204 No Content** : Succes sans contenu a renvoyer

#### 4xx - Erreurs client

- **400 Bad Request** : La requete est mal formee
- **401 Unauthorized** : Authentification requise
- **403 Forbidden** : Acces refuse
- **404 Not Found** : Ressource non trouvee
- **409 Conflict** : Conflit (ex: doublon)

#### 5xx - Erreurs serveur

- **500 Internal Server Error** : Erreur interne du serveur
- **503 Service Unavailable** : Service temporairement indisponible

### 6. POURQUOI JSON ?

#### Qu'est-ce que JSON ?

JSON (JavaScript Object Notation) est un format de donnees textuel. Il a ete introduit au debut des annees 2000.

#### Structure de JSON

```json
{
  "id": 1,
  "name": "Real Madrid",
  "country": "Spain",
  "players": ["Benzema", "Modric"],
  "active": true
}
```

#### Types de donnees en JSON

- **String (chaine)** : `"Real Madrid"`
- **Number (nombre)** : `123` ou `45.67`
- **Boolean (booleen)** : `true` ou `false`
- **Array (tableau)** : `["item1", "item2"]`
- **Object (objet)** : `{"key": "value"}`
- **null** : `null`

#### Avantages de JSON

**1. Simplicite**
- Facile a lire pour les humains
- Facile a parser pour les machines
- Structure claire et intuitive

**2. Leger**
- Moins verbeux que XML
- Taille reduite des messages
- Meilleure performance

**3. Compatibilite**
- Supporte par tous les langages de programmation
- Format natif de JavaScript
- Standard de l'industrie

**4. Flexibilite**
- Structure hierarchique
- Peut representer des donnees complexes
- Extensible facilement

#### Comparaison JSON vs XML

**XML (ancien format) :**
```xml
<equipe>
  <id>1</id>
  <name>Real Madrid</name>
  <country>Spain</country>
</equipe>
```

**JSON (format moderne) :**
```json
{
  "id": 1,
  "name": "Real Madrid",
  "country": "Spain"
}
```

JSON est 30% plus leger et plus facile a lire.

### 7. POSTMAN : OUTIL DE TEST D'API

#### Qu'est-ce que Postman ?

Postman est un outil qui permet de tester des API sans avoir besoin de creer une interface graphique. C'est l'outil le plus utilise par les developpeurs pour tester leurs API.

#### Pourquoi utiliser Postman ?

**1. Test rapide**
- Tester une API sans ecrire de code
- Voir immediatement les resultats
- Modifier facilement les parametres

**2. Organisation**
- Sauvegarder les requetes
- Creer des collections
- Partager avec l'equipe

**3. Documentation**
- Generer automatiquement la documentation
- Exemples de requetes
- Description des endpoints

**4. Automatisation**
- Creer des tests automatiques
- Executer des scenarios
- Verifier les reponses

#### Installation de Postman

1. Aller sur https://www.postman.com/downloads/
2. Telecharger la version pour votre systeme
3. Installer et lancer l'application
4. Creer un compte (gratuit)

#### Utilisation de base de Postman

**1. Creer une nouvelle requete**
- Cliquer sur "New" puis "HTTP Request"
- Ou utiliser le raccourci Ctrl+N

**2. Configurer la requete**
- Selectionner la methode (GET, POST, PUT, DELETE)
- Entrer l'URL : `http://localhost:3000/api/equipes`
- Ajouter des parametres si necessaire

**3. Pour une requete GET**
```
Method: GET
URL: http://localhost:3000/api/equipes
```
- Cliquer sur "Send"
- Observer la reponse dans la section inferieure

**4. Pour une requete POST**
```
Method: POST
URL: http://localhost:3000/api/equipes
```
- Aller dans l'onglet "Body"
- Selectionner "raw" et "JSON"
- Entrer les donnees :
```json
{
  "name": "Barcelona",
  "country": "Spain"
}
```
- Cliquer sur "Send"

**5. Lire la reponse**

La reponse contient :
- **Status** : Le code de statut (200, 201, 404, etc.)
- **Time** : Le temps de reponse en millisecondes
- **Size** : La taille de la reponse
- **Body** : Le contenu de la reponse en JSON

#### Exemple de test complet dans Postman

**Test 1 : Lister toutes les equipes**
```
GET http://localhost:3000/api/equipes
```
Resultat attendu : 200 OK avec la liste des equipes

**Test 2 : Creer une equipe**
```
POST http://localhost:3000/api/equipes
Body:
{
  "name": "PSG",
  "country": "France"
}
```
Resultat attendu : 201 Created avec l'equipe creee

**Test 3 : Modifier une equipe**
```
PUT http://localhost:3000/api/equipes/1
Body:
{
  "name": "Real Madrid CF",
  "country": "Spain"
}
```
Resultat attendu : 200 OK avec l'equipe modifiee

**Test 4 : Supprimer une equipe**
```
DELETE http://localhost:3000/api/equipes/1
```
Resultat attendu : 200 OK avec confirmation

### 8. STRUCTURE D'UNE REQUETE HTTP

#### Composants d'une requete

**1. La methode**
```
GET, POST, PUT, DELETE
```

**2. L'URL**
```
http://localhost:3000/api/equipes/1
```
- `http://` : Le protocole
- `localhost:3000` : L'adresse du serveur
- `/api/equipes/1` : Le chemin vers la ressource

**3. Les headers (en-tetes)**
```
Content-Type: application/json
Authorization: Bearer token123
```

**4. Le body (corps) - pour POST et PUT**
```json
{
  "name": "Barcelona",
  "country": "Spain"
}
```

#### Composants d'une reponse

**1. Le code de statut**
```
200 OK
```

**2. Les headers**
```
Content-Type: application/json
Date: Sat, 30 Nov 2025 10:00:00 GMT
```

**3. Le body**
```json
{
  "success": true,
  "message": "Equipe creee",
  "data": {
    "id": 1,
    "name": "Barcelona",
    "country": "Spain"
  }
}
```

### 9. BONNES PRATIQUES REST

#### Nomenclature des URL

**Utiliser des noms au pluriel**
```
Bon : /api/equipes
Mauvais : /api/equipe
```

**Utiliser des noms, pas des verbes**
```
Bon : GET /api/equipes
Mauvais : GET /api/getEquipes
```

**Hierarchie logique**
```
/api/equipes          - Liste des equipes
/api/equipes/1        - Une equipe specifique
/api/equipes/1/joueurs - Les joueurs d'une equipe
```

#### Format des reponses

**Toujours renvoyer un objet JSON**
```json
{
  "success": true,
  "message": "Operation reussie",
  "data": { ... }
}
```

**En cas d'erreur**
```json
{
  "success": false,
  "error": "Ressource non trouvee",
  "message": "L'equipe avec l'ID 999 n'existe pas"
}
```

#### Gestion des erreurs

- Utiliser les codes HTTP appropries
- Fournir des messages d'erreur clairs
- Ne pas exposer d'informations sensibles
- Logger les erreurs cote serveur

### 10. RESUME DES CONCEPTS

#### Ce qu'il faut retenir

1. **API REST** : Interface de communication entre applications via HTTP
2. **JSON** : Format leger et lisible pour echanger des donnees
3. **Methodes HTTP** : GET (lire), POST (creer), PUT (modifier), DELETE (supprimer)
4. **Codes de statut** : 2xx (succes), 4xx (erreur client), 5xx (erreur serveur)
5. **Postman** : Outil pour tester les API sans interface graphique
6. **Ressources** : Entites manipulees par l'API (equipes, utilisateurs, etc.)
7. **Endpoints** : URL qui permettent d'acceder aux ressources

---

## TD : ENONCE DU PROJET {#td}

### OBJECTIF GENERAL

Developper une API REST complete pour gerer deux types de ressources :
1. Des equipes de football
2. Des donnees de marche boursier

Cette API devra permettre de creer, lire, modifier et supprimer des donnees (operations CRUD).

### CONTEXTE

Vous travaillez pour une entreprise qui souhaite creer un systeme de gestion de donnees accessible via une API. Les donnees seront stockees dans des fichiers JSON et l'API devra etre testable via Postman et une interface web.

### SPECIFICATIONS FONCTIONNELLES

#### 1. Gestion des Equipes

**Ressource : Equipe**

Une equipe est caracterisee par :
- Un identifiant unique (id) - nombre entier
- Un nom (name) - chaine de caracteres
- Un pays (country) - chaine de caracteres
- Une date de creation (createdAt) - format ISO 8601

**Operations requises :**

1. **Lister toutes les equipes**
   - Methode : GET
   - Endpoint : `/api/equipes`
   - Reponse : Tableau de toutes les equipes avec leur nombre total

2. **Recuperer une equipe par son ID**
   - Methode : GET
   - Endpoint : `/api/equipes/:id`
   - Parametre : id (nombre)
   - Reponse : L'equipe correspondante ou erreur 404 si non trouvee

3. **Creer une nouvelle equipe**
   - Methode : POST
   - Endpoint : `/api/equipes`
   - Donnees requises : name, country
   - Validations :
     - Le nom ne doit pas etre vide
     - Le pays ne doit pas etre vide
     - Le nom ne doit pas exister deja (pas de doublon)
   - Reponse : L'equipe creee avec code 201

4. **Modifier une equipe existante**
   - Methode : PUT
   - Endpoint : `/api/equipes/:id`
   - Donnees requises : name, country
   - Validations : Memes validations que pour la creation
   - Reponse : L'equipe modifiee ou erreur 404

5. **Supprimer une equipe**
   - Methode : DELETE
   - Endpoint : `/api/equipes/:id`
   - Reponse : Confirmation de suppression ou erreur 404

#### 2. Gestion des Donnees Market

**Ressource : MarketData**

Une donnee market est caracterisee par :
- Un identifiant unique (id) - nombre entier
- Une cle boursiere (key) - chaine (ex: AAPL, GOOGL)
- Une valeur (value) - nombre decimal
- Un statut (status) - chaine (active, inactive, pending)
- Une date de creation (createdAt) - format ISO 8601
- Une date de derniere mise a jour (lastUpdated) - format ISO 8601

**Operations requises :**

1. **Lister toutes les donnees**
   - Methode : GET
   - Endpoint : `/api/market`
   - Filtres optionnels :
     - `?status=active` : Filtrer par statut
     - `?limit=5` : Limiter le nombre de resultats
   - Reponse : Tableau des donnees avec filtres appliques

2. **Recuperer une donnee par sa cle**
   - Methode : GET
   - Endpoint : `/api/market/:key`
   - Parametre : key (chaine)
   - Reponse : La donnee correspondante ou erreur 404

3. **Creer une nouvelle donnee**
   - Methode : POST
   - Endpoint : `/api/market`
   - Donnees requises : key, value, status (optionnel, par defaut "active")
   - Validations :
     - La cle ne doit pas etre vide
     - La valeur doit etre un nombre positif
     - Le statut doit etre active, inactive ou pending
     - La cle ne doit pas exister deja
   - Reponse : La donnee creee avec code 201

4. **Modifier une donnee existante**
   - Methode : PUT
   - Endpoint : `/api/market/:key`
   - Donnees : value et/ou status
   - Reponse : La donnee modifiee ou erreur 404

5. **Supprimer une donnee**
   - Methode : DELETE
   - Endpoint : `/api/market/:key`
   - Reponse : Confirmation de suppression ou erreur 404

### SPECIFICATIONS TECHNIQUES

#### 1. Technologies imposees

- **Node.js** : Version 14 ou superieure
- **Express.js** : Framework web pour Node.js
- **CORS** : Middleware pour autoriser les requetes cross-origin
- **File System (fs)** : Module Node.js pour la lecture/ecriture de fichiers

#### 2. Structure du projet

```
projet/
├── app.js                  # Fichier principal du serveur
├── package.json            # Configuration npm et dependances
├── data/                   # Dossier pour les donnees
│   ├── equipes.json        # Fichier JSON des equipes
│   └── marketData.json     # Fichier JSON des donnees market
├── routes/                 # Dossier pour les routes
│   ├── equipesRoutes.js    # Routes pour les equipes
│   └── marketRoutes.js     # Routes pour le market
└── public/                 # Dossier pour l'interface web
    ├── index.html          # Interface HTML
    └── script.js           # Code JavaScript client
```

#### 3. Format des reponses

Toutes les reponses doivent suivre ce format JSON :

**Succes :**
```json
{
  "success": true,
  "message": "Description de l'operation",
  "data": { ... },
  "count": 10  // Optionnel pour les listes
}
```

**Erreur :**
```json
{
  "success": false,
  "error": "Type d'erreur",
  "message": "Description detaillee",
  "details": []  // Optionnel pour les erreurs de validation
}
```

#### 4. Gestion des erreurs

L'API doit gerer les cas suivants :

1. **Route non trouvee (404)**
   - Message : "La route {methode} {url} n'existe pas"
   - Suggestion : "Consultez /api/docs"

2. **Donnees invalides (400)**
   - Liste des champs manquants ou invalides
   - Messages explicites

3. **Ressource non trouvee (404)**
   - Message : "Aucune ressource avec l'ID/cle {valeur}"

4. **Conflit (409)**
   - Message : "Une ressource avec ce nom/cette cle existe deja"

5. **Erreur serveur (500)**
   - Message : "Une erreur interne s'est produite"
   - Log de l'erreur dans la console

### LIVRABLES ATTENDUS

#### 1. Code source

- Fichier `app.js` configure et commente
- Fichiers de routes dans le dossier `routes/`
- Fichiers de donnees JSON initiaux
- Fichier `package.json` avec toutes les dependances

#### 2. Interface web

- Page HTML pour tester l'API
- Formulaires pour creer/modifier des ressources
- Affichage des reponses de l'API
- Design simple et fonctionnel

#### 3. Documentation

- Fichier `README.md` avec :
  - Instructions d'installation
  - Liste des endpoints
  - Exemples d'utilisation
- Commentaires dans le code source

### CRITERES D'EVALUATION

1. **Fonctionnalite (40%)**
   - Toutes les operations CRUD fonctionnent
   - Les validations sont correctes
   - Les codes de statut HTTP sont appropries

2. **Qualite du code (30%)**
   - Code structure et organise
   - Commentaires explicatifs
   - Gestion des erreurs

3. **Interface utilisateur (15%)**
   - Interface fonctionnelle
   - Facilite d'utilisation
   - Affichage clair des resultats

4. **Documentation (15%)**
   - README complet
   - Commentaires dans le code
   - Exemples d'utilisation

### CONSEILS POUR REUSSIR

1. **Commencez simple**
   - Creez d'abord le serveur de base
   - Ajoutez une route GET simple
   - Testez avant de continuer

2. **Testez regulierement**
   - Utilisez Postman apres chaque ajout
   - Verifiez les codes de statut
   - Controllez les donnees dans les fichiers JSON

3. **Organisez votre code**
   - Separez les routes dans des fichiers distincts
   - Creez des fonctions reutilisables
   - Commentez les parties complexes

4. **Gerez les erreurs**
   - Anticipez les cas d'erreur
   - Renvoyez des messages clairs
   - Utilisez try/catch pour la lecture/ecriture de fichiers

5. **Documentez au fur et a mesure**
   - Ajoutez des commentaires pendant le developpement
   - Notez les problemes rencontres
   - Mettez a jour le README

### QUESTIONS DE REFLEXION

Avant de commencer, reflechissez a :

1. Comment valider les donnees envoyees par le client ?
2. Comment eviter les doublons dans les donnees ?
3. Comment generer des ID uniques ?
4. Comment gerer les fichiers JSON de maniere securisee ?
5. Quelle structure de reponse sera la plus claire pour le client ?

### EXTENSIONS POSSIBLES (BONUS)

Si vous finissez avant la fin du TD, vous pouvez ajouter :

1. **Pagination**
   - Parametres `?page=1&limit=10`
   - Retourner le nombre total de pages

2. **Tri**
   - Parametres `?sortBy=name&order=asc`
   - Trier les resultats

3. **Recherche**
   - Parametre `?search=Real`
   - Rechercher dans les noms

4. **Statistiques**
   - Endpoint `/api/stats`
   - Nombre total de ressources, moyenne des valeurs, etc.

5. **Historique**
   - Sauvegarder les modifications
   - Endpoint `/api/equipes/:id/history`

---

## PREREQUIS POUR COMMENCER

### Connaissances necessaires

Avant de demarrer ce TD, vous devez :
- Savoir utiliser un terminal/ligne de commande
- Connaitre les bases de JavaScript (variables, fonctions, tableaux, objets)
- Comprendre le format JSON
- Avoir installe Node.js sur votre ordinateur

### Duree estimee

- Installation et configuration : 30 minutes
- Developpement du serveur de base : 1 heure
- Implementation des routes equipes : 1 heure 30
- Implementation des routes market : 1 heure
- Interface web : 1 heure
- Tests et validation : 30 minutes
- Documentation : 30 minutes

**Total : 6 heures**

### Demarrage

Vous etes maintenant pret a commencer le developpement. Suivez les etapes ci-dessous dans l'ordre.

---

## ETAPE 1 : INITIALISATION DU PROJET {#etape1}

### 1.1 Creation du dossier

Ouvrez votre terminal et executez :

```bash
# Creer le dossier du projet
mkdir api-rest-project
cd api-rest-project
```

### 1.2 Initialisation de npm

```bash
# Initialiser le projet Node.js
npm init -y
```

**Resultat attendu :** Un fichier `package.json` est cree avec la configuration par defaut.

### 1.3 Installation des dependances

```bash
# Installer Express et CORS
npm install express cors
```

**Explication :**
- `express` : Framework web pour Node.js
- `cors` : Permet les requetes cross-origin

### 1.4 Structure du projet

Creez la structure de dossiers :

```bash
# Windows
mkdir data routes public .vscode

# Linux/Mac
mkdir data routes public .vscode
```

Vous devriez avoir :
```
api-rest-project/
├── data/
├── routes/
├── public/
├── .vscode/
├── package.json
└── node_modules/
```

---

## ETAPE 2 : CREATION DU SERVEUR DE BASE {#etape2}

### 2.1 Creation du fichier principal

Creez un fichier `app.js` a la racine :

```javascript
/**
 * PROJET PEDAGOGIQUE - API REST avec Node.js
 * Fichier principal : Configuration du serveur
 */

// Importation des modules
const express = require('express');
const cors = require('cors');

// Creation de l'application Express
const app = express();
const port = 3000;

// Configuration des middlewares
app.use(express.json());  // Parser le JSON
app.use(cors());          // Autoriser CORS
app.use(express.static('public'));  // Fichiers statiques

// Route de test
app.get('/', (req, res) => {
    res.json({
        message: 'API REST - Projet Pedagogique',
        version: '1.0.0',
        endpoints: {
            equipes: '/api/equipes',
            market: '/api/market'
        }
    });
});

// Demarrage du serveur
app.listen(port, () => {
    console.log('==========================================');
    console.log('SERVEUR API REST DEMARRE');
    console.log('==========================================');
    console.log(`API: http://localhost:${port}`);
    console.log('==========================================');
});
```

### 2.2 Test du serveur

```bash
# Demarrer le serveur
node app.js
```

**Test :** Ouvrez http://localhost:3000 dans votre navigateur.

**Resultat attendu :** Vous devez voir un objet JSON avec le message de bienvenue.

### 2.3 Ajout d'un script dans package.json

Modifiez `package.json` pour ajouter le script de demarrage :

```json
{
  "name": "api-rest-project",
  "version": "1.0.0",
  "main": "app.js",
  "scripts": {
    "start": "node app.js",
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "dependencies": {
    "express": "^5.1.0",
    "cors": "^2.8.5"
  }
}
```

Maintenant vous pouvez demarrer avec `npm start`.

### 2.4 Commandes Node.js utiles

Voici les commandes Node.js essentielles pour votre projet :

#### Commandes de base

```bash
# Demarrer le serveur
node app.js

# Demarrer avec npm (utilise le script dans package.json)
npm start

# Arreter le serveur
# Appuyez sur Ctrl + C dans le terminal
```

#### Commandes npm

```bash
# Initialiser un nouveau projet Node.js
npm init
# Avec valeurs par defaut (pas de questions)
npm init -y

# Installer une dependance
npm install express
npm install express cors

# Installer une dependance en mode developpement
npm install --save-dev nodemon

# Installer toutes les dependances du package.json
npm install

# Desinstaller une dependance
npm uninstall express

# Voir la liste des dependances installees
npm list
npm list --depth=0  # Voir seulement les dependances principales

# Mettre a jour les dependances
npm update

# Verifier les dependances obsoletes
npm outdated

# Nettoyer le cache npm
npm cache clean --force
```

#### Commandes pour le debogage

```bash
# Demarrer en mode debug
node --inspect app.js

# Voir la version de Node.js
node --version
node -v

# Voir la version de npm
npm --version
npm -v

# Executer du code JavaScript directement
node -e "console.log('Hello')"

# Ouvrir le REPL (Read-Eval-Print Loop)
node
# Tapez .exit pour quitter
```

#### Commandes pour la gestion des fichiers

```bash
# Windows PowerShell
# Creer un fichier
New-Item app.js -ItemType File

# Creer un dossier
New-Item data -ItemType Directory

# Supprimer un fichier
Remove-Item app.js

# Supprimer un dossier et son contenu
Remove-Item node_modules -Recurse -Force

# Afficher le contenu d'un fichier
Get-Content app.js

# Lister les fichiers
Get-ChildItem
dir  # Raccourci
```

#### Scripts personnalises dans package.json

Vous pouvez ajouter des scripts personnalises :

```json
{
  "scripts": {
    "start": "node app.js",
    "dev": "nodemon app.js",
    "test": "node test.js",
    "clean": "rm -rf node_modules"
  }
}
```

Puis les executer avec :
```bash
npm run start
npm run dev
npm run test
npm run clean
```

#### Commandes pour nodemon (auto-restart)

Si vous installez nodemon (outil de developpement) :

```bash
# Installer nodemon globalement
npm install -g nodemon

# Ou localement dans le projet
npm install --save-dev nodemon

# Demarrer avec nodemon (redemarre automatiquement)
nodemon app.js

# Ou via npm script
npm run dev
```

#### Variables d'environnement

```bash
# Windows PowerShell
$env:PORT=3000
node app.js

# Utiliser dans le code
const port = process.env.PORT || 3000;
```

#### Informations systeme

```bash
# Voir le chemin de Node.js
where.exe node  # Windows
which node      # Linux/Mac

# Voir les variables d'environnement
$env:PATH  # Windows PowerShell
echo $PATH  # Linux/Mac

# Voir l'architecture du systeme
node -p "process.arch"

# Voir la plateforme
node -p "process.platform"
```

#### Commandes de gestion de processus

```bash
# Voir les processus Node.js en cours
# Windows PowerShell
Get-Process node

# Tuer un processus par PID
Stop-Process -Id 1234

# Tuer tous les processus Node.js
Get-Process node | Stop-Process
```

#### Conseils pratiques

1. **Toujours verifier que le serveur est arrete** avant de relancer
2. **Utiliser `npm start`** plutot que `node app.js` pour la coherence
3. **Installer nodemon** pour le developpement (redemarrage automatique)
4. **Lire les messages d'erreur** attentivement dans la console
5. **Verifier le port** : assurez-vous que le port 3000 est libre

---

## ETAPE 3 : STRUCTURE DES DONNEES {#etape3}

### 3.1 Fichier equipes.json

Creez `data/equipes.json` :

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
    },
    {
        "id": 3,
        "name": "Bayern Munich",
        "country": "Germany",
        "createdAt": "2025-01-01T10:00:00.000Z"
    }
]
```

**Explication :**
- `id` : Identifiant unique (nombre)
- `name` : Nom de l'equipe (texte)
- `country` : Pays (texte)
- `createdAt` : Date de creation (format ISO)

### 3.2 Fichier marketData.json

Creez `data/marketData.json` :

```json
[
    {
        "id": 101,
        "key": "AAPL",
        "value": 150.50,
        "status": "active",
        "createdAt": "2025-01-01T10:00:00.000Z",
        "lastUpdated": "2025-01-01T10:00:00.000Z"
    },
    {
        "id": 102,
        "key": "GOOGL",
        "value": 2800.10,
        "status": "active",
        "createdAt": "2025-01-01T10:00:00.000Z",
        "lastUpdated": "2025-01-01T10:00:00.000Z"
    },
    {
        "id": 103,
        "key": "TSLA",
        "value": 220.75,
        "status": "inactive",
        "createdAt": "2025-01-01T10:00:00.000Z",
        "lastUpdated": "2025-01-01T10:00:00.000Z"
    }
]
```

**Explication :**
- `id` : Identifiant unique
- `key` : Cle boursiere (ex: AAPL pour Apple)
- `value` : Valeur numerique
- `status` : Statut (active, inactive, pending)
- `lastUpdated` : Date de derniere modification

---

## ETAPE 4 : ROUTES POUR LES EQUIPES {#etape4}

### 4.1 Fonctions utilitaires

Creez `routes/equipesRoutes.js` et commencez par les fonctions de base :

```javascript
/**
 * ROUTES EQUIPES - CRUD COMPLET
 */

const express = require('express');
const router = express.Router();
const fs = require('fs');
const path = require('path');

// Chemin vers le fichier JSON
const equipesPath = path.join(__dirname, '../data/equipes.json');

/**
 * FONCTIONS UTILITAIRES
 */

// Lire les equipes depuis le fichier
function lireEquipes() {
    try {
        const data = fs.readFileSync(equipesPath, 'utf8');
        return JSON.parse(data);
    } catch (error) {
        console.error('Erreur lecture:', error);
        return [];
    }
}

// Ecrire les equipes dans le fichier
function ecrireEquipes(equipes) {
    try {
        fs.writeFileSync(equipesPath, JSON.stringify(equipes, null, 4));
        return true;
    } catch (error) {
        console.error('Erreur ecriture:', error);
        return false;
    }
}

// Valider une equipe
function validerEquipe(equipe) {
    const erreurs = [];
    
    if (!equipe.name || equipe.name.trim() === '') {
        erreurs.push('Le nom est requis');
    }
    
    if (!equipe.country || equipe.country.trim() === '') {
        erreurs.push('Le pays est requis');
    }
    
    return erreurs;
}
```

**Explication detaillee :**

1. **lireEquipes()** :
   - Lit le fichier JSON
   - Parse le contenu en objet JavaScript
   - Retourne un tableau vide en cas d'erreur

2. **ecrireEquipes()** :
   - Convertit l'objet en JSON
   - Ecrit dans le fichier avec indentation
   - Retourne true/false selon le succes

3. **validerEquipe()** :
   - Verifie que les champs requis sont presents
   - Retourne un tableau d'erreurs

### 4.2 Route GET - Lister toutes les equipes

Ajoutez apres les fonctions utilitaires :

```javascript
/**
 * GET /api/equipes
 * Recuperer toutes les equipes
 */
router.get('/', (req, res) => {
    console.log('Lecture de toutes les equipes');
    
    const equipes = lireEquipes();
    
    res.status(200).json({
        success: true,
        message: 'Equipes recuperees avec succes',
        count: equipes.length,
        data: equipes
    });
});
```

**Decomposition :**
- `router.get('/')` : Definit une route GET
- `console.log` : Log pour le debuggage
- `lireEquipes()` : Recupere les donnees
- `res.status(200)` : Code HTTP 200 (OK)
- `res.json()` : Envoie la reponse en JSON

### 4.3 Route GET - Une equipe par ID

```javascript
/**
 * GET /api/equipes/:id
 * Recuperer une equipe specifique
 */
router.get('/:id', (req, res) => {
    const id = parseInt(req.params.id);
    console.log(`Recherche equipe ID: ${id}`);
    
    // Validation de l'ID
    if (isNaN(id)) {
        return res.status(400).json({
            success: false,
            error: 'ID invalide',
            message: 'L\'ID doit etre un nombre'
        });
    }
    
    const equipes = lireEquipes();
    const equipe = equipes.find(e => e.id === id);
    
    if (!equipe) {
        return res.status(404).json({
            success: false,
            error: 'Equipe non trouvee',
            message: `Aucune equipe avec l'ID ${id}`
        });
    }
    
    res.status(200).json({
        success: true,
        message: 'Equipe trouvee',
        data: equipe
    });
});
```

**Points cles :**
- `:id` : Parametre dynamique dans l'URL
- `parseInt()` : Conversion en nombre
- `isNaN()` : Verification si c'est un nombre
- `find()` : Recherche dans le tableau
- Code 404 si non trouve

### 4.4 Route POST - Creer une equipe

```javascript
/**
 * POST /api/equipes
 * Creer une nouvelle equipe
 */
router.post('/', (req, res) => {
    console.log('Creation nouvelle equipe');
    console.log('Donnees recues:', req.body);
    
    const { name, country } = req.body;
    
    // Validation
    const erreurs = validerEquipe({ name, country });
    if (erreurs.length > 0) {
        return res.status(400).json({
            success: false,
            error: 'Donnees invalides',
            details: erreurs
        });
    }
    
    const equipes = lireEquipes();
    
    // Verifier si l'equipe existe deja
    const existe = equipes.find(e => 
        e.name.toLowerCase() === name.toLowerCase()
    );
    
    if (existe) {
        return res.status(409).json({
            success: false,
            error: 'Conflit',
            message: 'Une equipe avec ce nom existe deja'
        });
    }
    
    // Creer la nouvelle equipe
    const nouvelId = equipes.length > 0 
        ? Math.max(...equipes.map(e => e.id)) + 1 
        : 1;
    
    const nouvelleEquipe = {
        id: nouvelId,
        name: name.trim(),
        country: country.trim(),
        createdAt: new Date().toISOString()
    };
    
    equipes.push(nouvelleEquipe);
    
    // Sauvegarder
    if (!ecrireEquipes(equipes)) {
        return res.status(500).json({
            success: false,
            error: 'Erreur serveur',
            message: 'Impossible de sauvegarder'
        });
    }
    
    console.log('Equipe creee:', nouvelleEquipe.name);
    
    res.status(201).json({
        success: true,
        message: 'Equipe creee avec succes',
        data: nouvelleEquipe
    });
});
```

**Explication detaillee :**

1. **Recuperation des donnees** : `req.body` contient les donnees envoyees
2. **Validation** : Utilise la fonction `validerEquipe()`
3. **Verification doublon** : Compare les noms (insensible a la casse)
4. **Generation ID** : Prend le max + 1
5. **Creation objet** : Structure avec tous les champs
6. **Sauvegarde** : Ecrit dans le fichier JSON
7. **Code 201** : Created (ressource creee)

### 4.5 Route PUT - Modifier une equipe

```javascript
/**
 * PUT /api/equipes/:id
 * Modifier une equipe existante
 */
router.put('/:id', (req, res) => {
    const id = parseInt(req.params.id);
    console.log(`Modification equipe ID: ${id}`);
    
    if (isNaN(id)) {
        return res.status(400).json({
            success: false,
            error: 'ID invalide'
        });
    }
    
    const { name, country } = req.body;
    
    // Validation
    const erreurs = validerEquipe({ name, country });
    if (erreurs.length > 0) {
        return res.status(400).json({
            success: false,
            error: 'Donnees invalides',
            details: erreurs
        });
    }
    
    const equipes = lireEquipes();
    const index = equipes.findIndex(e => e.id === id);
    
    if (index === -1) {
        return res.status(404).json({
            success: false,
            error: 'Equipe non trouvee'
        });
    }
    
    // Mise a jour
    equipes[index] = {
        ...equipes[index],
        name: name.trim(),
        country: country.trim(),
        updatedAt: new Date().toISOString()
    };
    
    if (!ecrireEquipes(equipes)) {
        return res.status(500).json({
            success: false,
            error: 'Erreur de sauvegarde'
        });
    }
    
    console.log('Equipe mise a jour:', equipes[index].name);
    
    res.status(200).json({
        success: true,
        message: 'Equipe mise a jour',
        data: equipes[index]
    });
});
```

**Points importants :**
- `findIndex()` : Trouve la position dans le tableau
- Spread operator `...` : Conserve les anciennes valeurs
- `updatedAt` : Marque la date de modification

### 4.6 Route DELETE - Supprimer une equipe

```javascript
/**
 * DELETE /api/equipes/:id
 * Supprimer une equipe
 */
router.delete('/:id', (req, res) => {
    const id = parseInt(req.params.id);
    console.log(`Suppression equipe ID: ${id}`);
    
    if (isNaN(id)) {
        return res.status(400).json({
            success: false,
            error: 'ID invalide'
        });
    }
    
    const equipes = lireEquipes();
    const index = equipes.findIndex(e => e.id === id);
    
    if (index === -1) {
        return res.status(404).json({
            success: false,
            error: 'Equipe non trouvee'
        });
    }
    
    // Sauvegarder avant suppression
    const equipeSupprimee = equipes[index];
    
    // Supprimer
    equipes.splice(index, 1);
    
    if (!ecrireEquipes(equipes)) {
        return res.status(500).json({
            success: false,
            error: 'Erreur de sauvegarde'
        });
    }
    
    console.log('Equipe supprimee:', equipeSupprimee.name);
    
    res.status(200).json({
        success: true,
        message: 'Equipe supprimee',
        data: equipeSupprimee
    });
});

// Exporter le router
module.exports = router;
```

**Explication :**
- `splice(index, 1)` : Supprime 1 element a l'index donne
- Retourne l'equipe supprimee pour confirmation

### 4.7 Integration dans app.js

Modifiez `app.js` pour ajouter les routes :

```javascript
// Apres les imports au debut
const equipesRouter = require('./routes/equipesRoutes');

// Apres les middlewares
app.use('/api/equipes', equipesRouter);
```

### 4.8 Test des routes equipes

Redemarrez le serveur et testez :

```bash
# Dans le navigateur ou Postman
GET http://localhost:3000/api/equipes
GET http://localhost:3000/api/equipes/1

# Avec Postman (POST)
POST http://localhost:3000/api/equipes
Body (JSON):
{
    "name": "Paris Saint-Germain",
    "country": "France"
}
```

---

## ETAPE 5 : ROUTES POUR LE MARKET {#etape5}

### 5.1 Creation du fichier marketRoutes.js

Creez `routes/marketRoutes.js` avec la meme structure :

```javascript
/**
 * ROUTES MARKET - CRUD AVANCE
 */

const express = require('express');
const router = express.Router();
const fs = require('fs');
const path = require('path');

const marketDataPath = path.join(__dirname, '../data/marketData.json');

// Fonctions utilitaires (similaires aux equipes)
function lireMarketData() {
    try {
        const data = fs.readFileSync(marketDataPath, 'utf8');
        return JSON.parse(data);
    } catch (error) {
        console.error('Erreur lecture market:', error);
        return [];
    }
}

function ecrireMarketData(data) {
    try {
        fs.writeFileSync(marketDataPath, JSON.stringify(data, null, 4));
        return true;
    } catch (error) {
        console.error('Erreur ecriture market:', error);
        return false;
    }
}

function validerMarketData(data) {
    const erreurs = [];
    
    if (!data.key || data.key.trim() === '') {
        erreurs.push('La cle est requise');
    }
    
    if (data.value === undefined || isNaN(data.value) || data.value < 0) {
        erreurs.push('La valeur doit etre un nombre positif');
    }
    
    if (data.status && !['active', 'inactive', 'pending'].includes(data.status)) {
        erreurs.push('Statut invalide');
    }
    
    return erreurs;
}
```

### 5.2 Routes GET avec filtres

```javascript
/**
 * GET /api/market
 * Avec support des parametres de requete (query params)
 */
router.get('/', (req, res) => {
    console.log('Lecture donnees market');
    console.log('Parametres:', req.query);
    
    let marketData = lireMarketData();
    
    // Filtre par status
    if (req.query.status) {
        marketData = marketData.filter(item => 
            item.status === req.query.status
        );
    }
    
    // Limitation des resultats
    if (req.query.limit) {
        const limit = parseInt(req.query.limit);
        if (!isNaN(limit) && limit > 0) {
            marketData = marketData.slice(0, limit);
        }
    }
    
    res.status(200).json({
        success: true,
        message: 'Donnees recuperees',
        count: marketData.length,
        filters: req.query,
        data: marketData
    });
});

/**
 * GET /api/market/:key
 * Recherche par cle (ex: AAPL)
 */
router.get('/:key', (req, res) => {
    const key = req.params.key.toUpperCase();
    console.log(`Recherche market: ${key}`);
    
    const marketData = lireMarketData();
    const item = marketData.find(d => d.key.toUpperCase() === key);
    
    if (!item) {
        return res.status(404).json({
            success: false,
            error: 'Donnees non trouvees',
            message: `Aucune donnee pour la cle ${key}`
        });
    }
    
    res.status(200).json({
        success: true,
        data: item
    });
});
```

**Nouveaute :** Les query params permettent de filtrer (`?status=active&limit=5`)

### 5.3 Routes POST, PUT, DELETE

Ajoutez les autres routes (similaires aux equipes mais avec la cle au lieu de l'ID) :

```javascript
/**
 * POST /api/market
 */
router.post('/', (req, res) => {
    const { key, value, status = 'active' } = req.body;
    
    const erreurs = validerMarketData({ key, value, status });
    if (erreurs.length > 0) {
        return res.status(400).json({
            success: false,
            error: 'Donnees invalides',
            details: erreurs
        });
    }
    
    const marketData = lireMarketData();
    
    // Verifier si la cle existe
    const existe = marketData.find(d => 
        d.key.toUpperCase() === key.toUpperCase()
    );
    
    if (existe) {
        return res.status(409).json({
            success: false,
            error: 'Conflit',
            message: 'Cette cle existe deja'
        });
    }
    
    const nouvelId = marketData.length > 0 
        ? Math.max(...marketData.map(d => d.id)) + 1 
        : 101;
    
    const nouvelItem = {
        id: nouvelId,
        key: key.toUpperCase(),
        value: parseFloat(value),
        status: status,
        createdAt: new Date().toISOString(),
        lastUpdated: new Date().toISOString()
    };
    
    marketData.push(nouvelItem);
    
    if (!ecrireMarketData(marketData)) {
        return res.status(500).json({
            success: false,
            error: 'Erreur de sauvegarde'
        });
    }
    
    res.status(201).json({
        success: true,
        message: 'Donnees creees',
        data: nouvelItem
    });
});

/**
 * PUT /api/market/:key
 */
router.put('/:key', (req, res) => {
    const key = req.params.key.toUpperCase();
    const { value, status } = req.body;
    
    const marketData = lireMarketData();
    const itemIndex = marketData.findIndex(d => 
        d.key.toUpperCase() === key
    );
    
    if (itemIndex === -1) {
        return res.status(404).json({
            success: false,
            error: 'Donnees non trouvees'
        });
    }
    
    // Mise a jour partielle
    if (value !== undefined) {
        marketData[itemIndex].value = parseFloat(value);
    }
    if (status !== undefined) {
        marketData[itemIndex].status = status;
    }
    marketData[itemIndex].lastUpdated = new Date().toISOString();
    
    if (!ecrireMarketData(marketData)) {
        return res.status(500).json({
            success: false,
            error: 'Erreur de sauvegarde'
        });
    }
    
    res.status(200).json({
        success: true,
        message: 'Donnees mises a jour',
        data: marketData[itemIndex]
    });
});

/**
 * DELETE /api/market/:key
 */
router.delete('/:key', (req, res) => {
    const key = req.params.key.toUpperCase();
    
    const marketData = lireMarketData();
    const itemIndex = marketData.findIndex(d => 
        d.key.toUpperCase() === key
    );
    
    if (itemIndex === -1) {
        return res.status(404).json({
            success: false,
            error: 'Donnees non trouvees'
        });
    }
    
    const itemSupprime = marketData[itemIndex];
    marketData.splice(itemIndex, 1);
    
    if (!ecrireMarketData(marketData)) {
        return res.status(500).json({
            success: false,
            error: 'Erreur de sauvegarde'
        });
    }
    
    res.status(200).json({
        success: true,
        message: 'Donnees supprimees',
        data: itemSupprime
    });
});

module.exports = router;
```

### 5.4 Integration dans app.js

Ajoutez dans `app.js` :

```javascript
const marketRouter = require('./routes/marketRoutes');
app.use('/api/market', marketRouter);
```

---

## ETAPE 6 : GESTION DES ERREURS {#etape6}

### 6.1 Middleware de logging

Dans `app.js`, ajoutez apres les autres middlewares :

```javascript
// Middleware de logging
app.use((req, res, next) => {
    console.log(`${new Date().toISOString()} - ${req.method} ${req.url}`);
    next();
});
```

### 6.2 Route de documentation interactive

Ajoutez une route de documentation complete et detaillee :

```javascript
// Route de documentation complete
app.get('/api/docs', (req, res) => {
    res.json({
        title: 'API REST - Documentation Complete',
        version: '1.0.0',
        description: 'API pedagogique pour apprendre les concepts REST',
        baseURL: 'http://localhost:3000',
        
        // Documentation des equipes
        equipes: {
            description: 'Gestion des equipes de football',
            endpoints: [
                {
                    method: 'GET',
                    path: '/api/equipes',
                    description: 'Recuperer toutes les equipes',
                    parameters: 'Aucun',
                    requestBody: 'Aucun',
                    responseExample: {
                        success: true,
                        message: 'Equipes recuperees avec succes',
                        count: 3,
                        data: [
                            {
                                id: 1,
                                name: 'Real Madrid',
                                country: 'Spain',
                                createdAt: '2025-01-01T10:00:00.000Z'
                            }
                        ]
                    },
                    statusCodes: {
                        200: 'Succes'
                    }
                },
                {
                    method: 'GET',
                    path: '/api/equipes/:id',
                    description: 'Recuperer une equipe par son ID',
                    parameters: 'id (nombre) - Identifiant de l\'equipe',
                    requestBody: 'Aucun',
                    responseExample: {
                        success: true,
                        message: 'Equipe trouvee',
                        data: {
                            id: 1,
                            name: 'Real Madrid',
                            country: 'Spain',
                            createdAt: '2025-01-01T10:00:00.000Z'
                        }
                    },
                    statusCodes: {
                        200: 'Equipe trouvee',
                        400: 'ID invalide',
                        404: 'Equipe non trouvee'
                    }
                },
                {
                    method: 'POST',
                    path: '/api/equipes',
                    description: 'Creer une nouvelle equipe',
                    parameters: 'Aucun',
                    requestBody: {
                        name: 'string (requis) - Nom de l\'equipe',
                        country: 'string (requis) - Pays de l\'equipe'
                    },
                    requestExample: {
                        name: 'Barcelona',
                        country: 'Spain'
                    },
                    responseExample: {
                        success: true,
                        message: 'Equipe creee avec succes',
                        data: {
                            id: 4,
                            name: 'Barcelona',
                            country: 'Spain',
                            createdAt: '2025-11-30T10:00:00.000Z'
                        }
                    },
                    statusCodes: {
                        201: 'Equipe creee',
                        400: 'Donnees invalides',
                        409: 'Equipe existe deja'
                    }
                },
                {
                    method: 'PUT',
                    path: '/api/equipes/:id',
                    description: 'Modifier une equipe existante',
                    parameters: 'id (nombre) - Identifiant de l\'equipe',
                    requestBody: {
                        name: 'string (requis) - Nouveau nom',
                        country: 'string (requis) - Nouveau pays'
                    },
                    requestExample: {
                        name: 'Real Madrid CF',
                        country: 'Spain'
                    },
                    responseExample: {
                        success: true,
                        message: 'Equipe mise a jour',
                        data: {
                            id: 1,
                            name: 'Real Madrid CF',
                            country: 'Spain',
                            createdAt: '2025-01-01T10:00:00.000Z',
                            updatedAt: '2025-11-30T10:00:00.000Z'
                        }
                    },
                    statusCodes: {
                        200: 'Equipe modifiee',
                        400: 'Donnees invalides',
                        404: 'Equipe non trouvee'
                    }
                },
                {
                    method: 'DELETE',
                    path: '/api/equipes/:id',
                    description: 'Supprimer une equipe',
                    parameters: 'id (nombre) - Identifiant de l\'equipe',
                    requestBody: 'Aucun',
                    responseExample: {
                        success: true,
                        message: 'Equipe supprimee',
                        data: {
                            id: 1,
                            name: 'Real Madrid',
                            country: 'Spain',
                            createdAt: '2025-01-01T10:00:00.000Z'
                        }
                    },
                    statusCodes: {
                        200: 'Equipe supprimee',
                        400: 'ID invalide',
                        404: 'Equipe non trouvee'
                    }
                }
            ]
        },
        
        // Documentation du market
        market: {
            description: 'Gestion des donnees de marche boursier',
            endpoints: [
                {
                    method: 'GET',
                    path: '/api/market',
                    description: 'Recuperer toutes les donnees market',
                    parameters: 'status (optionnel) - Filtrer par statut (active, inactive, pending)\nlimit (optionnel) - Limiter le nombre de resultats',
                    requestBody: 'Aucun',
                    exampleURL: '/api/market?status=active&limit=5',
                    responseExample: {
                        success: true,
                        message: 'Donnees recuperees',
                        count: 2,
                        filters: { status: 'active', limit: '5' },
                        data: [
                            {
                                id: 101,
                                key: 'AAPL',
                                value: 150.50,
                                status: 'active',
                                createdAt: '2025-01-01T10:00:00.000Z',
                                lastUpdated: '2025-01-01T10:00:00.000Z'
                            }
                        ]
                    },
                    statusCodes: {
                        200: 'Succes'
                    }
                },
                {
                    method: 'GET',
                    path: '/api/market/:key',
                    description: 'Recuperer une donnee par sa cle boursiere',
                    parameters: 'key (string) - Cle boursiere (ex: AAPL, GOOGL)',
                    requestBody: 'Aucun',
                    responseExample: {
                        success: true,
                        data: {
                            id: 101,
                            key: 'AAPL',
                            value: 150.50,
                            status: 'active',
                            createdAt: '2025-01-01T10:00:00.000Z',
                            lastUpdated: '2025-01-01T10:00:00.000Z'
                        }
                    },
                    statusCodes: {
                        200: 'Donnee trouvee',
                        404: 'Donnee non trouvee'
                    }
                },
                {
                    method: 'POST',
                    path: '/api/market',
                    description: 'Creer une nouvelle donnee market',
                    parameters: 'Aucun',
                    requestBody: {
                        key: 'string (requis) - Cle boursiere',
                        value: 'number (requis) - Valeur numerique positive',
                        status: 'string (optionnel) - Statut (active, inactive, pending)'
                    },
                    requestExample: {
                        key: 'MSFT',
                        value: 380.50,
                        status: 'active'
                    },
                    responseExample: {
                        success: true,
                        message: 'Donnees creees',
                        data: {
                            id: 104,
                            key: 'MSFT',
                            value: 380.50,
                            status: 'active',
                            createdAt: '2025-11-30T10:00:00.000Z',
                            lastUpdated: '2025-11-30T10:00:00.000Z'
                        }
                    },
                    statusCodes: {
                        201: 'Donnee creee',
                        400: 'Donnees invalides',
                        409: 'Cle existe deja'
                    }
                },
                {
                    method: 'PUT',
                    path: '/api/market/:key',
                    description: 'Modifier une donnee market existante',
                    parameters: 'key (string) - Cle boursiere',
                    requestBody: {
                        value: 'number (optionnel) - Nouvelle valeur',
                        status: 'string (optionnel) - Nouveau statut'
                    },
                    requestExample: {
                        value: 155.75,
                        status: 'active'
                    },
                    responseExample: {
                        success: true,
                        message: 'Donnees mises a jour',
                        data: {
                            id: 101,
                            key: 'AAPL',
                            value: 155.75,
                            status: 'active',
                            createdAt: '2025-01-01T10:00:00.000Z',
                            lastUpdated: '2025-11-30T10:00:00.000Z'
                        }
                    },
                    statusCodes: {
                        200: 'Donnee modifiee',
                        404: 'Donnee non trouvee'
                    }
                },
                {
                    method: 'DELETE',
                    path: '/api/market/:key',
                    description: 'Supprimer une donnee market',
                    parameters: 'key (string) - Cle boursiere',
                    requestBody: 'Aucun',
                    responseExample: {
                        success: true,
                        message: 'Donnees supprimees',
                        data: {
                            id: 101,
                            key: 'AAPL',
                            value: 150.50,
                            status: 'active',
                            createdAt: '2025-01-01T10:00:00.000Z',
                            lastUpdated: '2025-01-01T10:00:00.000Z'
                        }
                    },
                    statusCodes: {
                        200: 'Donnee supprimee',
                        404: 'Donnee non trouvee'
                    }
                }
            ]
        },
        
        // Codes de statut HTTP
        httpStatusCodes: {
            200: 'OK - La requete a reussi',
            201: 'Created - Ressource creee avec succes',
            400: 'Bad Request - Donnees invalides ou manquantes',
            404: 'Not Found - Ressource non trouvee',
            409: 'Conflict - Conflit (ressource existe deja)',
            500: 'Internal Server Error - Erreur interne du serveur'
        },
        
        // Format des reponses
        responseFormat: {
            success: {
                success: true,
                message: 'Description de l\'operation',
                data: 'Donnees retournees',
                count: 'Nombre d\'elements (pour les listes)'
            },
            error: {
                success: false,
                error: 'Type d\'erreur',
                message: 'Description detaillee',
                details: 'Tableau d\'erreurs (pour validation)'
            }
        },
        
        // Instructions pour Postman
        postmanInstructions: {
            step1: 'Ouvrir Postman',
            step2: 'Creer une nouvelle requete',
            step3: 'Selectionner la methode HTTP (GET, POST, PUT, DELETE)',
            step4: 'Entrer l\'URL complete (ex: http://localhost:3000/api/equipes)',
            step5: 'Pour POST/PUT: Aller dans Body > raw > JSON et entrer les donnees',
            step6: 'Cliquer sur Send',
            step7: 'Observer la reponse (status, body, time)'
        },
        
        // Conseils d'utilisation
        tips: [
            'Utilisez cette documentation comme reference lors de vos tests',
            'Testez chaque endpoint dans l\'ordre: GET, POST, PUT, DELETE',
            'Verifiez toujours les codes de statut HTTP',
            'Consultez les exemples de requetes et reponses',
            'Utilisez Postman pour tester facilement l\'API'
        ]
    });
});
```

**Explication de cette route :**

Cette route `/api/docs` retourne une documentation JSON complete et structuree qui contient :

1. **Informations generales** : Titre, version, description, URL de base
2. **Documentation detaillee pour chaque endpoint** avec :
   - Methode HTTP
   - Chemin de l'endpoint
   - Description
   - Parametres requis
   - Exemple de requete
   - Exemple de reponse
   - Codes de statut possibles
3. **Format des reponses** : Structure standard des reponses succes et erreur
4. **Guide Postman** : Instructions etape par etape
5. **Conseils d'utilisation**

Les eleves peuvent acceder a cette documentation via :
- `http://localhost:3000/api/docs` directement dans le navigateur
- Via l'interface web que nous allons ameliorer

**Important pour les eleves :** Cette route `/api/docs` est votre reference principale. A chaque fois que vous avez un doute sur :
- Comment utiliser un endpoint
- Quel format de donnees envoyer
- Quels codes de statut attendre
- Comment tester avec Postman

Visitez `http://localhost:3000/api/docs` pour voir toute la documentation avec des exemples concrets.

### 6.3 Gestion des routes non trouvees

```javascript
// Route 404 - Doit etre avant le gestionnaire d'erreurs
app.use((req, res) => {
    res.status(404).json({
        error: 'Route non trouvee',
        message: `La route ${req.method} ${req.originalUrl} n'existe pas`,
        suggestion: 'Consultez /api/docs'
    });
});
```

### 6.4 Gestionnaire d'erreurs global

```javascript
// Gestionnaire d'erreurs - Doit etre en dernier
app.use((err, req, res, next) => {
    console.error('Erreur:', err.stack);
    res.status(500).json({
        error: 'Erreur interne du serveur',
        message: 'Une erreur inattendue s\'est produite'
    });
});
```

### 6.5 Message de demarrage ameliore

Remplacez le `app.listen()` par :

```javascript
app.listen(port, () => {
    console.log('==========================================');
    console.log('SERVEUR API REST DEMARRE');
    console.log('==========================================');
    console.log(`API: http://localhost:${port}`);
    console.log(`Documentation: http://localhost:${port}/api/docs`);
    console.log('==========================================');
});
```

---

## ETAPE 7 : INTERFACE WEB {#etape7}

### 7.1 Creation du fichier HTML

Creez `public/index.html` (version simplifiee sans emoji) :

```html
<!DOCTYPE html>
<html lang="fr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>API REST - Interface de Test</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }
        
        body {
            font-family: Arial, sans-serif;
            background: #f4f4f4;
            color: #333;
        }
        
        .container {
            max-width: 1200px;
            margin: 0 auto;
            padding: 20px;
        }
        
        .header {
            background: #333;
            color: white;
            padding: 20px;
            text-align: center;
            margin-bottom: 30px;
        }
        
        .section {
            background: white;
            padding: 30px;
            margin-bottom: 30px;
            border-radius: 5px;
            box-shadow: 0 2px 5px rgba(0,0,0,0.1);
        }
        
        .grid {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
            gap: 20px;
        }
        
        .card {
            border: 1px solid #ddd;
            padding: 20px;
            border-radius: 5px;
            background: #f9f9f9;
        }
        
        .method-badge {
            display: inline-block;
            padding: 4px 8px;
            border-radius: 3px;
            font-size: 0.8em;
            font-weight: bold;
            color: white;
            margin-left: 10px;
        }
        
        .GET { background: #28a745; }
        .POST { background: #007bff; }
        .PUT { background: #ffc107; color: #333; }
        .DELETE { background: #dc3545; }
        
        .form-control {
            width: 100%;
            padding: 8px;
            margin: 10px 0;
            border: 1px solid #ddd;
            border-radius: 3px;
        }
        
        .btn {
            width: 100%;
            padding: 10px;
            border: none;
            border-radius: 3px;
            cursor: pointer;
            font-size: 14px;
            margin-top: 10px;
        }
        
        .btn-success { background: #28a745; color: white; }
        .btn-primary { background: #007bff; color: white; }
        .btn-warning { background: #ffc107; color: #333; }
        .btn-danger { background: #dc3545; color: white; }
        
        .response-area {
            background: #f8f9fa;
            border: 1px solid #ddd;
            padding: 20px;
            border-radius: 3px;
            font-family: monospace;
            white-space: pre-wrap;
            max-height: 400px;
            overflow-y: auto;
        }
        
        .status-badge {
            display: inline-block;
            padding: 5px 10px;
            border-radius: 3px;
            font-weight: bold;
            color: white;
            margin-bottom: 15px;
        }
        
        .status-200, .status-201 { background: #28a745; }
        .status-400 { background: #ffc107; color: #333; }
        .status-404, .status-500 { background: #dc3545; }
    </style>
</head>
<body>
    <div class="header">
        <h1>API REST - Interface de Test</h1>
        <p>Testez vos endpoints facilement</p>
    </div>

    <div class="container">
        <div class="section">
            <h2>Equipes</h2>
            <div class="grid">
                <div class="card">
                    <h3>Lister toutes
                        <span class="method-badge GET">GET</span>
                    </h3>
                    <button class="btn btn-success" 
                            onclick="executeRequest('GET', '/api/equipes', null)">
                        Executer
                    </button>
                </div>
                
                <div class="card">
                    <h3>Par ID
                        <span class="method-badge GET">GET</span>
                    </h3>
                    <input type="number" id="equipeId" 
                           class="form-control" placeholder="ID" value="1">
                    <button class="btn btn-success" onclick="getEquipeById()">
                        Rechercher
                    </button>
                </div>
                
                <div class="card">
                    <h3>Creer
                        <span class="method-badge POST">POST</span>
                    </h3>
                    <input type="text" id="equipeName" 
                           class="form-control" placeholder="Nom">
                    <input type="text" id="equipeCountry" 
                           class="form-control" placeholder="Pays">
                    <button class="btn btn-primary" onclick="createEquipe()">
                        Creer
                    </button>
                </div>
            </div>
        </div>

        <div class="section">
            <h2>Reponse de l'API</h2>
            <span id="responseStatus" class="status-badge" 
                  style="background: #6c757d;">Aucune requete</span>
            <pre id="responseArea" class="response-area">Aucune reponse pour le moment.</pre>
        </div>
    </div>

    <script src="script.js"></script>
</body>
</html>
```

### 7.2 Creation du fichier JavaScript

Creez `public/script.js` :

```javascript
// Configuration
const API_BASE_URL = 'http://localhost:3000';

// Elements DOM
const responseArea = document.getElementById('responseArea');
const responseStatus = document.getElementById('responseStatus');

// Fonction principale d'execution
async function executeRequest(method, endpoint, body = null) {
    const startTime = Date.now();
    
    try {
        const options = {
            method: method,
            headers: {
                'Content-Type': 'application/json',
            },
        };
        
        if (body) {
            options.body = JSON.stringify(body);
        }
        
        const response = await fetch(`${API_BASE_URL}${endpoint}`, options);
        const duration = Date.now() - startTime;
        const data = await response.json();
        
        // Afficher la reponse
        displayResponse(data, response.status, duration);
        
    } catch (error) {
        console.error('Erreur:', error);
        displayResponse({
            error: 'Erreur de connexion',
            message: 'Impossible de contacter l\'API'
        }, 500, 0);
    }
}

// Afficher la reponse
function displayResponse(response, status, duration) {
    responseStatus.className = `status-badge status-${status}`;
    responseStatus.textContent = `${status} (${duration}ms)`;
    responseArea.textContent = JSON.stringify(response, null, 2);
}

// Fonctions specifiques
function getEquipeById() {
    const id = document.getElementById('equipeId').value;
    if (!id) {
        alert('Veuillez entrer un ID');
        return;
    }
    executeRequest('GET', `/api/equipes/${id}`, null);
}

function createEquipe() {
    const name = document.getElementById('equipeName').value;
    const country = document.getElementById('equipeCountry').value;
    
    if (!name || !country) {
        alert('Veuillez remplir tous les champs');
        return;
    }
    
    const body = { name, country };
    executeRequest('POST', '/api/equipes', body);
}
```

### 7.3 Integration dans app.js

L'integration est deja faite avec :
```javascript
app.use(express.static('public'));
```

Ajoutez une route de redirection :
```javascript
app.get('/interface', (req, res) => {
    res.redirect('/index.html');
});
```

### 7.4 Test de l'interface

Ouvrez http://localhost:3000/interface dans votre navigateur.

---

## ETAPE 8 : TESTS ET VALIDATION {#etape8}

### 8.1 Tests manuels

Creez un fichier `tests.md` avec les scenarios de test :

```markdown
# SCENARIOS DE TEST

## Test 1 : GET Toutes les equipes
- URL: GET http://localhost:3000/api/equipes
- Resultat attendu: 200, liste des equipes

## Test 2 : GET Equipe par ID
- URL: GET http://localhost:3000/api/equipes/1
- Resultat attendu: 200, equipe Real Madrid

## Test 3 : POST Nouvelle equipe
- URL: POST http://localhost:3000/api/equipes
- Body: {"name": "PSG", "country": "France"}
- Resultat attendu: 201, equipe creee

## Test 4 : Erreur 404
- URL: GET http://localhost:3000/api/equipes/999
- Resultat attendu: 404, equipe non trouvee

## Test 5 : Erreur 400
- URL: POST http://localhost:3000/api/equipes
- Body: {"name": "", "country": ""}
- Resultat attendu: 400, donnees invalides
```

### 8.2 Verification des fichiers

Votre projet doit maintenant contenir :

```
api-rest-project/
├── app.js
├── package.json
├── data/
│   ├── equipes.json
│   └── marketData.json
├── routes/
│   ├── equipesRoutes.js
│   └── marketRoutes.js
├── public/
│   ├── index.html
│   └── script.js
└── node_modules/
```

### 8.3 Verification du code

Utilisez cette checklist :

- [ ] Le serveur demarre sans erreur
- [ ] GET /api/equipes retourne les donnees
- [ ] POST /api/equipes cree une equipe
- [ ] PUT /api/equipes/:id modifie une equipe
- [ ] DELETE /api/equipes/:id supprime une equipe
- [ ] Les memes operations fonctionnent pour /api/market
- [ ] L'interface web est accessible
- [ ] Les erreurs sont gerees correctement

---

## ETAPE 9 : DOCUMENTATION {#etape9}

### 9.1 Fichier README.md

Creez un fichier `README.md` complet :

```markdown
# API REST - Projet Pedagogique

## Description

API REST complete pour apprendre les concepts fondamentaux avec Node.js et Express.

## Installation

```bash
# Cloner le projet
git clone [url]

# Installer les dependances
npm install

# Demarrer le serveur
npm start
```

## Utilisation

- API : http://localhost:3000
- Interface web : http://localhost:3000/interface
- Documentation : http://localhost:3000/api/docs

## Endpoints

### Equipes
- GET /api/equipes - Lister toutes
- GET /api/equipes/:id - Une par ID
- POST /api/equipes - Creer
- PUT /api/equipes/:id - Modifier
- DELETE /api/equipes/:id - Supprimer

### Market
- GET /api/market - Lister toutes
- GET /api/market/:key - Une par cle
- POST /api/market - Creer
- PUT /api/market/:key - Modifier
- DELETE /api/market/:key - Supprimer

## Technologies

- Node.js
- Express.js
- CORS

## Auteur

Projet pedagogique
```

### 9.2 Commentaires dans le code

Assurez-vous que chaque fonction a un commentaire explicatif.

### 9.3 Guide d'utilisation

Creez un fichier `UTILISATION.md` :

```markdown
# GUIDE D'UTILISATION

## Demarrage

1. Ouvrir un terminal
2. Naviguer vers le dossier du projet
3. Executer `npm start`
4. Ouvrir http://localhost:3000/interface

## Tests avec l'interface

### Creer une equipe
1. Aller dans la section Equipes
2. Remplir les champs Nom et Pays
3. Cliquer sur Creer
4. Observer la reponse

### Lister les equipes
1. Cliquer sur le bouton "Executer" dans "Lister toutes"
2. Observer la liste dans la zone de reponse

## Tests avec Postman

### Configuration
1. Importer la collection (si disponible)
2. URL de base : http://localhost:3000

### Exemple POST
```
POST http://localhost:3000/api/equipes
Content-Type: application/json

{
    "name": "Barcelona",
    "country": "Spain"
}
```

## Troubleshooting

### Le serveur ne demarre pas
- Verifier que le port 3000 est libre
- Verifier que Node.js est installe
- Executer `npm install`

### Erreur 404
- Verifier l'URL
- Consulter /api/docs pour les routes disponibles

### Donnees non sauvegardees
- Verifier les permissions sur le dossier data/
- Consulter les logs dans la console
```

---

## ANNEXES

### A. Codes de statut HTTP

| Code | Nom | Description |
|------|-----|-------------|
| 200 | OK | Succes |
| 201 | Created | Ressource creee |
| 400 | Bad Request | Donnees invalides |
| 404 | Not Found | Ressource non trouvee |
| 409 | Conflict | Conflit (doublon) |
| 500 | Server Error | Erreur serveur |

### B. Structure JSON des reponses

Toutes les reponses suivent ce format :

```json
{
    "success": true|false,
    "message": "Description",
    "data": { ... },
    "error": "Description erreur"
}
```

### C. Commandes utiles

#### Commandes Node.js et npm

```bash
# Gestion du serveur
npm start                    # Demarrer le serveur
node app.js                  # Demarrer directement avec Node
Ctrl + C                     # Arreter le serveur

# Gestion des dependances
npm install                  # Installer toutes les dependances
npm install express          # Installer une dependance specifique
npm install --save-dev nodemon  # Installer en mode developpement
npm uninstall express        # Desinstaller une dependance
npm update                   # Mettre a jour les dependances
npm outdated                 # Voir les dependances obsoletes

# Informations et verification
node --version              # Version de Node.js
npm --version               # Version de npm
npm list                    # Liste des dependances
npm list --depth=0          # Dependances principales seulement

# Debogage et tests
node --inspect app.js       # Mode debug
npm test                    # Executer les tests
npm run dev                 # Script personnalise (si configure)

# Nettoyage
npm cache clean --force     # Nettoyer le cache npm
Remove-Item node_modules -Recurse -Force  # Supprimer node_modules (Windows)
rm -rf node_modules         # Supprimer node_modules (Linux/Mac)
```

#### Commandes PowerShell (Windows)

```powershell
# Gestion des fichiers
New-Item app.js -ItemType File           # Creer un fichier
New-Item data -ItemType Directory        # Creer un dossier
Remove-Item app.js                       # Supprimer un fichier
Get-Content app.js                       # Afficher le contenu
Get-ChildItem                            # Lister les fichiers

# Navigation
cd nom-dossier              # Changer de repertoire
cd ..                       # Remonter d'un niveau
pwd                         # Afficher le repertoire courant

# Processus
Get-Process node            # Voir les processus Node.js
Stop-Process -Id 1234       # Tuer un processus par ID
Get-Process node | Stop-Process  # Tuer tous les processus Node

# Variables d'environnement
$env:PORT=3000              # Definir une variable
$env:PORT                   # Afficher une variable
```

#### Commandes Git (gestion de version)

```bash
# Initialisation
git init                    # Initialiser un depot Git
git clone [url]             # Cloner un projet

# Gestion des changements
git status                  # Voir l'etat des fichiers
git add .                   # Ajouter tous les fichiers
git add app.js              # Ajouter un fichier specifique
git commit -m "Message"     # Enregistrer les changements
git push                    # Envoyer vers le serveur distant
git pull                    # Recuperer les changements

# Branches
git branch                  # Lister les branches
git branch nom-branche      # Creer une branche
git checkout nom-branche    # Changer de branche
git merge nom-branche       # Fusionner une branche
```

### D. Extensions VS Code recommandees

- REST Client
- Thunder Client
- ESLint
- Prettier

### E. Ressources complementaires

- Documentation Express : https://expressjs.com
- REST API Tutorial : https://restfulapi.net
- HTTP Status Codes : https://httpstatuses.com
- Node.js Documentation : https://nodejs.org

---

## CONCLUSION

Felicitations ! Vous avez construit une API REST complete de A a Z.

### Competences acquises

- Creation d'un serveur Express
- Implementation CRUD complete
- Gestion des erreurs
- Validation des donnees
- Interface web
- Organisation du code
- Documentation

### Prochaines etapes

1. Ajouter une base de donnees (MongoDB, PostgreSQL)
2. Implementer l'authentification (JWT)
3. Ajouter des tests unitaires (Jest)
4. Deployer sur un serveur (Heroku, AWS)
5. Ajouter Swagger pour la documentation
6. Implementer la pagination
7. Ajouter des filtres avances
8. Gerer les uploads de fichiers

### Ressources pour aller plus loin

- **Bases de donnees** : MongoDB, PostgreSQL, MySQL
- **Authentification** : JWT, Passport.js, OAuth
- **Tests** : Jest, Mocha, Supertest
- **Documentation** : Swagger/OpenAPI
- **Deployment** : Heroku, AWS, DigitalOcean
- **Securite** : Helmet, Rate Limiting
- **Performance** : Redis, Caching

---

## GLOSSAIRE COMPLET {#glossaire}

### A

**API (Application Programming Interface)**
Interface qui permet a deux applications de communiquer entre elles. C'est un ensemble de regles qui definit comment des logiciels peuvent echanger des donnees.

**Asynchrone**
Mode d'execution ou une operation peut se derouler sans bloquer le reste du programme. En JavaScript, les operations asynchrones utilisent des callbacks, promises ou async/await.

**Array (Tableau)**
Structure de donnees qui stocke une collection d'elements ordonnes. En JavaScript : `[1, 2, 3]`

### B

**Backend**
Partie d'une application qui s'execute sur le serveur. Elle gere la logique metier, les bases de donnees et les API.

**Body (Corps)**
Partie d'une requete HTTP qui contient les donnees envoyees au serveur. Utilise principalement avec POST et PUT.

**Boolean (Booleen)**
Type de donnee qui peut avoir deux valeurs : `true` (vrai) ou `false` (faux).

### C

**Callback**
Fonction passee en parametre a une autre fonction, qui sera executee plus tard (souvent apres une operation asynchrone).

**Cache**
Mecanisme de stockage temporaire des donnees pour ameliorer les performances en evitant de refaire les memes operations.

**CORS (Cross-Origin Resource Sharing)**
Mecanisme de securite qui permet a une page web d'acceder a des ressources d'un autre domaine.

**CRUD**
Acronyme pour Create (Creer), Read (Lire), Update (Mettre a jour), Delete (Supprimer). Les quatre operations de base sur les donnees.

**Client**
Application ou programme qui envoie des requetes a un serveur. Peut etre un navigateur web, une application mobile, Postman, etc.

### D

**Dependance**
Package ou bibliotheque externe dont votre projet a besoin pour fonctionner. Liste dans package.json.

**Destructuring (Destructuration)**
Syntaxe JavaScript qui permet d'extraire des valeurs d'objets ou de tableaux : `const { name, country } = req.body`

### E

**Endpoint**
URL specifique d'une API qui permet d'acceder a une ressource. Exemple : `/api/equipes`

**Express**
Framework web pour Node.js qui simplifie la creation de serveurs et d'API.

**Error (Erreur)**
Probleme qui survient lors de l'execution du code. Peut etre gere avec try/catch.

### F

**Fetch**
Fonction JavaScript moderne pour faire des requetes HTTP depuis le navigateur.

**File System (fs)**
Module Node.js qui permet de lire et ecrire des fichiers sur le disque dur.

**Frontend**
Partie d'une application qui s'execute dans le navigateur de l'utilisateur. Interface visuelle avec laquelle l'utilisateur interagit.

**Fonction**
Bloc de code reutilisable qui effectue une tache specifique. Peut recevoir des parametres et retourner une valeur.

### G

**GET**
Methode HTTP utilisee pour recuperer des donnees depuis un serveur sans les modifier.

### H

**HTTP (HyperText Transfer Protocol)**
Protocole de communication utilise sur Internet pour echanger des informations entre client et serveur.

**Headers (En-tetes)**
Informations supplementaires envoyees avec une requete ou une reponse HTTP. Exemple : `Content-Type: application/json`

### I

**ID (Identifiant)**
Valeur unique qui permet d'identifier une ressource specifique. Generalement un nombre ou une chaine de caracteres.

**Interface**
Point de connexion entre deux systemes. Dans le contexte web, c'est la partie visuelle avec laquelle l'utilisateur interagit.

**ISO 8601**
Format standard international pour representer les dates et heures : `2025-11-30T10:00:00.000Z`

### J

**JavaScript**
Langage de programmation utilise pour le web. Peut s'executer dans le navigateur (frontend) ou sur le serveur avec Node.js (backend).

**JSON (JavaScript Object Notation)**
Format de donnees textuel leger et lisible, utilise pour echanger des informations. Exemple : `{"name": "Real Madrid"}`

**JWT (JSON Web Token)**
Standard pour creer des tokens d'authentification securises.

### K

**Key (Cle)**
Dans un objet JSON, c'est le nom d'une propriete. Dans `{"name": "Madrid"}`, "name" est la cle.

### L

**Localhost**
Adresse qui designe votre propre ordinateur. `http://localhost:3000` signifie "mon serveur local sur le port 3000".

**Logging**
Action d'enregistrer des informations sur l'execution du programme, generalement avec `console.log()`.

### M

**Methode HTTP**
Type d'action a effectuer sur une ressource : GET, POST, PUT, DELETE, PATCH, etc.

**Middleware**
Fonction qui s'execute entre la reception d'une requete et l'envoi de la reponse. Exemple : `express.json()` pour parser le JSON.

**Module**
Fichier JavaScript qui exporte des fonctionnalites pour etre utilise dans d'autres fichiers.

### N

**Node.js**
Environnement d'execution JavaScript cote serveur. Permet d'utiliser JavaScript pour creer des applications backend.

**npm (Node Package Manager)**
Gestionnaire de packages pour Node.js. Permet d'installer, gerer et partager des bibliotheques JavaScript.

**null**
Valeur speciale qui represente l'absence intentionnelle de valeur.

### O

**Objet**
Structure de donnees qui stocke des paires cle-valeur. Exemple : `{name: "Madrid", country: "Spain"}`

### P

**Package**
Bibliotheque ou module externe que vous pouvez installer avec npm.

**package.json**
Fichier de configuration d'un projet Node.js qui contient les informations sur le projet et ses dependances.

**Parametre**
Valeur dynamique dans l'URL d'un endpoint. Exemple : dans `/api/equipes/:id`, `:id` est un parametre.

**Parse (Parser)**
Convertir des donnees d'un format a un autre. `JSON.parse()` convertit du texte JSON en objet JavaScript.

**Path (Chemin)**
Localisation d'un fichier ou d'une ressource. Peut etre relatif ou absolu.

**POST**
Methode HTTP utilisee pour creer une nouvelle ressource sur le serveur.

**Postman**
Outil de test d'API qui permet d'envoyer des requetes HTTP et de voir les reponses.

**Promise**
Objet JavaScript qui represente l'achevement ou l'echec futur d'une operation asynchrone.

**PUT**
Methode HTTP utilisee pour modifier une ressource existante sur le serveur.

### Q

**Query Parameters (Parametres de requete)**
Parametres ajoutes a la fin d'une URL apres `?`. Exemple : `/api/market?status=active&limit=5`

### R

**REST (Representational State Transfer)**
Style d'architecture pour concevoir des API web basees sur HTTP.

**Route**
Definition d'un endpoint avec sa methode HTTP et sa fonction de traitement.

**Router**
Objet Express qui gere un groupe de routes. Permet d'organiser les routes par ressource.

**Request (Requete)**
Message envoye par le client au serveur pour demander une action.

**Response (Reponse)**
Message renvoye par le serveur au client apres avoir traite une requete.

**REPL (Read-Eval-Print Loop)**
Interface interactive pour executer du code JavaScript dans le terminal avec `node`.

### S

**Serveur**
Ordinateur ou programme qui recoit des requetes et renvoie des reponses.

**Status Code (Code de statut)**
Nombre a trois chiffres qui indique le resultat d'une requete HTTP (200, 404, 500, etc.).

**String (Chaine de caracteres)**
Type de donnee qui represente du texte. En JavaScript : `"Hello World"`

**Stringify**
Convertir un objet JavaScript en chaine JSON avec `JSON.stringify()`.

**Synchrone**
Mode d'execution ou chaque operation doit se terminer avant que la suivante ne commence.

### T

**Terminal**
Interface en ligne de commande pour interagir avec l'ordinateur en tapant des commandes textuelles.

**Token**
Identifiant unique utilise pour l'authentification et l'autorisation.

**try/catch**
Structure de gestion des erreurs en JavaScript. Le code dans `try` est execute, si une erreur survient, le code dans `catch` est execute.

### U

**URL (Uniform Resource Locator)**
Adresse complete d'une ressource sur Internet. Exemple : `http://localhost:3000/api/equipes`

**undefined**
Valeur speciale qui indique qu'une variable n'a pas encore ete initialisee.

### V

**Validation**
Processus de verification que les donnees respectent certaines regles avant de les traiter.

**Variable**
Conteneur qui stocke une valeur. Declaration avec `const`, `let` ou `var`.

### W

**Webhook**
Mecanisme permettant a une application d'envoyer automatiquement des donnees a une autre application quand un evenement se produit.

### X

**XML (eXtensible Markup Language)**
Ancien format de donnees utilise avant JSON. Plus verbeux et complexe.

### Symboles et Operateurs

**=>**
Fleche de fonction arrow (fonction flechee) en JavaScript : `(param) => { code }`

**...**
Spread operator (operateur de propagation) : permet de copier ou fusionner des objets/tableaux.

**?**
Operateur ternaire : `condition ? siVrai : siFaux`

**async/await**
Mots-cles pour gerer les operations asynchrones de maniere plus lisible.

**===**
Operateur de comparaison stricte (valeur et type).

**!==**
Operateur de difference stricte.

---

**Bon apprentissage et bon developpement !**
```
