# GUIDE COMPLET - CONTENEURISATION AVEC DOCKER ET KUBERNETES

## TABLE DES MATIERES

1. [PARTIE 1 : COURS THEORIQUE](#partie1)
   - Qu'est-ce que Docker ?
   - Qu'est-ce qu'un conteneur ?
   - Docker vs Machine Virtuelle
   - Architecture Docker
   - Qu'est-ce que Kubernetes ?
   - Docker Compose
   - Concepts essentiels

2. [PARTIE 2 : INSTALLATION](#partie2)
   - Installation Docker Desktop sur Windows
   - Installation Docker sur Linux
   - Installation Docker Desktop sur macOS
   - Verification de l'installation

3. [PARTIE 3 : CONTENEURISATION DE L'API REST](#partie3)
   - Creation du Dockerfile
   - Creation du .dockerignore
   - Construction de l'image Docker
   - Execution du conteneur
   - Tests de l'application conteneurisee

4. [PARTIE 4 : DOCKER COMPOSE](#partie4)
   - Qu'est-ce que Docker Compose ?
   - Creation du docker-compose.yml
   - Orchestration multi-conteneurs
   - Variables d'environnement

5. [PARTIE 5 : KUBERNETES (K8S)](#partie5)
   - Installation de Minikube
   - Concepts Kubernetes (Pods, Services, Deployments)
   - Creation des fichiers de configuration K8s
   - Deploiement sur Kubernetes
   - Scaling et Load Balancing

6. [PARTIE 6 : BONNES PRATIQUES](#partie6)
   - Optimisation des images Docker
   - Securite des conteneurs
   - Logging et monitoring
   - CI/CD avec Docker

---

# PARTIE 1 : COURS THEORIQUE {#partie1}

## 1. QU'EST-CE QUE DOCKER ?

### Definition simple

**Docker** est une plateforme qui permet de **packager** une application et toutes ses dependances dans un **conteneur** leger et portable.

### Analogie du conteneur maritime

```
┌─────────────────────────────────────┐
│  CONTENEUR MARITIME                 │
│  ┌─────────┐  ┌─────────┐          │
│  │ Produit │  │ Produit │          │
│  │    A    │  │    B    │          │
│  └─────────┘  └─────────┘          │
│  Transport standardise               │
└─────────────────────────────────────┘
         ↓ SIMILAIRE ↓
┌─────────────────────────────────────┐
│  CONTENEUR DOCKER                   │
│  ┌─────────┐  ┌─────────┐          │
│  │  App +  │  │  App +  │          │
│  │  Deps   │  │  Deps   │          │
│  └─────────┘  └─────────┘          │
│  Deployment standardise             │
└─────────────────────────────────────┘
```

### Pourquoi utiliser Docker ?

✅ **Portabilite** : "Fonctionne sur ma machine" → "Fonctionne partout"
✅ **Isolation** : Chaque conteneur est independant
✅ **Leger** : Demarre en quelques secondes
✅ **Reproductibilite** : Meme environnement partout
✅ **Facilite de deploiement** : Un simple `docker run`

---

## 2. QU'EST-CE QU'UN CONTENEUR ?

### Definition

Un **conteneur** est une unite logicielle qui emballe le code d'une application et toutes ses dependances pour qu'elle s'execute rapidement et de maniere fiable d'un environnement informatique a un autre.

### Contenu d'un conteneur

```
┌─────────────────────────────────┐
│      CONTENEUR DOCKER           │
│                                 │
│  ┌───────────────────────────┐ │
│  │   Votre Application       │ │
│  │   (app.js, routes, etc.)  │ │
│  └───────────────────────────┘ │
│  ┌───────────────────────────┐ │
│  │   Dependances NPM         │ │
│  │   (express, etc.)         │ │
│  └───────────────────────────┘ │
│  ┌───────────────────────────┐ │
│  │   Runtime Node.js         │ │
│  └───────────────────────────┘ │
│  ┌───────────────────────────┐ │
│  │   Systeme de base         │ │
│  │   (Linux Alpine)          │ │
│  └───────────────────────────┘ │
└─────────────────────────────────┘
```

---

## 3. DOCKER VS MACHINE VIRTUELLE

### Comparaison visuelle

```
MACHINE VIRTUELLE (VM)              CONTENEUR DOCKER
┌─────────────────────┐            ┌─────────────────────┐
│      App A          │            │      App A          │
│  ┌──────────────┐   │            ├─────────────────────┤
│  │ Dependances  │   │            │      App B          │
│  └──────────────┘   │            ├─────────────────────┤
│  ┌──────────────┐   │            │      App C          │
│  │   OS Invite  │   │            ├─────────────────────┤
│  │   (Linux)    │   │            │   Docker Engine     │
│  └──────────────┘   │            ├─────────────────────┤
├─────────────────────┤            │   OS Hote           │
│      App B          │            └─────────────────────┘
│  ┌──────────────┐   │
│  │ Dependances  │   │
│  └──────────────┘   │
│  ┌──────────────┐   │
│  │   OS Invite  │   │
│  │   (Windows)  │   │
│  └──────────────┘   │
├─────────────────────┤
│    Hyperviseur      │
├─────────────────────┤
│      OS Hote        │
└─────────────────────┘
```

### Differences cles

| Critere | Machine Virtuelle | Conteneur Docker |
|---------|-------------------|------------------|
| **Taille** | Plusieurs Go | Quelques Mo |
| **Demarrage** | Minutes | Secondes |
| **Isolation** | Complete (OS complet) | Partage le kernel |
| **Performance** | Plus lente | Quasi-native |
| **Portabilite** | Moyenne | Excellente |

---

## 4. ARCHITECTURE DOCKER

### Les composants principaux

```
┌──────────────────────────────────────────────┐
│           DOCKER ARCHITECTURE                │
│                                              │
│  ┌────────────┐         ┌─────────────────┐ │
│  │   CLIENT   │────────▶│  DOCKER DAEMON  │ │
│  │            │         │   (dockerd)     │ │
│  │  docker    │  REST   │                 │ │
│  │  commands  │   API   │  - Build        │ │
│  └────────────┘         │  - Run          │ │
│                         │  - Distribute   │ │
│                         └─────────────────┘ │
│                                ↓             │
│                    ┌───────────────────────┐ │
│                    │   CONTENEURS          │ │
│                    │  ┌─────┐  ┌─────┐    │ │
│                    │  │ C1  │  │ C2  │    │ │
│                    │  └─────┘  └─────┘    │ │
│                    └───────────────────────┘ │
│                                ↓             │
│                    ┌───────────────────────┐ │
│                    │   IMAGES              │ │
│                    │  ┌─────┐  ┌─────┐    │ │
│                    │  │ I1  │  │ I2  │    │ │
│                    │  └─────┘  └─────┘    │ │
│                    └───────────────────────┘ │
│                                              │
│            ↕ Docker Hub (Registry)           │
└──────────────────────────────────────────────┘
```

### Concepts cles

#### 1. **Image Docker**
- Template en lecture seule
- Contient le code, les dependances, les bibliotheques
- Comparable a une "classe" en POO

#### 2. **Conteneur Docker**
- Instance executable d'une image
- Comparable a un "objet" en POO
- Peut etre demarre, arrete, supprime

#### 3. **Dockerfile**
- Fichier de recette pour construire une image
- Instructions pas a pas

#### 4. **Docker Hub**
- Registry public d'images Docker
- Comme GitHub mais pour les images Docker

---

## 5. QU'EST-CE QUE KUBERNETES ?

### Definition simple

**Kubernetes (K8s)** est une plateforme d'orchestration de conteneurs qui automatise le deploiement, la mise a l'echelle et la gestion des applications conteneurisees.

### Analogie du chef d'orchestre

```
     ORCHESTRE MUSICAL              KUBERNETES
┌─────────────────────────┐   ┌─────────────────────────┐
│   Chef d'orchestre      │   │   K8s Control Plane     │
│   (Coordonne tout)      │   │   (Gere les conteneurs) │
└─────────────────────────┘   └─────────────────────────┘
           ↓                              ↓
  ┌────────────────────┐        ┌────────────────────┐
  │  Section violons   │        │   Pod 1 (3 replicas)│
  │  Section cuivres   │        │   Pod 2 (2 replicas)│
  │  Section percussions│       │   Pod 3 (1 replica) │
  └────────────────────┘        └────────────────────┘
```

### Pourquoi Kubernetes ?

✅ **Auto-healing** : Redemarrage automatique des conteneurs defaillants
✅ **Scaling** : Ajustement automatique du nombre de conteneurs
✅ **Load Balancing** : Repartition du trafic
✅ **Rolling Updates** : Mises a jour sans interruption
✅ **Service Discovery** : Les services se trouvent automatiquement

---

## 6. DOCKER COMPOSE

### Definition

**Docker Compose** est un outil pour definir et executer des applications Docker multi-conteneurs.

### Exemple d'architecture multi-conteneurs

```
┌────────────────────────────────────────┐
│      APPLICATION COMPLETE              │
│                                        │
│  ┌──────────────┐  ┌──────────────┐  │
│  │  Conteneur   │  │  Conteneur   │  │
│  │   Node.js    │◄─┤   MongoDB    │  │
│  │   (API)      │  │   (DB)       │  │
│  └──────────────┘  └──────────────┘  │
│         ↑                             │
│  ┌──────────────┐                    │
│  │  Conteneur   │                    │
│  │    Nginx     │                    │
│  │ (Reverse     │                    │
│  │  Proxy)      │                    │
│  └──────────────┘                    │
└────────────────────────────────────────┘
```

---

## 7. CONCEPTS ESSENTIELS

### Workflow Docker de base

```
1. ECRIRE                2. CONSTRUIRE           3. EXECUTER
┌──────────┐            ┌──────────┐            ┌──────────┐
│Dockerfile│  docker    │  Image   │  docker    │Conteneur │
│          │  build     │          │  run       │          │
│          │───────────▶│          │───────────▶│          │
└──────────┘            └──────────┘            └──────────┘
```

### Commandes Docker essentielles

| Commande | Description |
|----------|-------------|
| `docker build` | Construire une image |
| `docker run` | Executer un conteneur |
| `docker ps` | Lister les conteneurs actifs |
| `docker images` | Lister les images |
| `docker stop` | Arreter un conteneur |
| `docker rm` | Supprimer un conteneur |
| `docker rmi` | Supprimer une image |
| `docker logs` | Voir les logs d'un conteneur |

---

# PARTIE 2 : INSTALLATION {#partie2}

## 1. INSTALLATION DOCKER DESKTOP SUR WINDOWS

### Pre-requis

- Windows 10/11 (64-bit)
- WSL 2 (Windows Subsystem for Linux)
- Virtualisation activee dans le BIOS

### Etape 1 : Activer WSL 2

Ouvrez PowerShell en mode administrateur et executez :

```powershell
# Activer WSL
wsl --install

# Redemarrer votre ordinateur apres cette commande
```

Apres le redemarrage, verifiez WSL :

```powershell
# Verifier la version WSL
wsl --list --verbose

# Mettre a jour WSL
wsl --update
```

### Etape 2 : Telecharger Docker Desktop

1. Allez sur : https://www.docker.com/products/docker-desktop
2. Cliquez sur **"Download for Windows"**
3. Executez le fichier `Docker Desktop Installer.exe`

### Etape 3 : Installation

1. Lancez l'installateur
2. Cochez **"Use WSL 2 instead of Hyper-V"** (recommande)
3. Suivez les instructions
4. Redemarrez votre ordinateur si demande

### Etape 4 : Configuration initiale

1. Lancez Docker Desktop
2. Acceptez les termes et conditions
3. Connectez-vous avec votre compte Docker Hub (optionnel)
4. Docker Desktop va demarrer le daemon

### Etape 5 : Verification

Ouvrez PowerShell et executez :

```powershell
# Verifier la version de Docker
docker --version
# Sortie attendue : Docker version 24.x.x, build xxxxx

# Verifier que Docker fonctionne
docker run hello-world
# Si tout fonctionne, vous verrez un message de bienvenue
```

### Configuration WSL 2 (Important)

```powershell
# Lister les distributions WSL
wsl --list --verbose

# Definir WSL 2 par defaut
wsl --set-default-version 2

# Si vous avez Ubuntu, definissez-le en WSL 2
wsl --set-version Ubuntu 2
```

---

## 2. INSTALLATION DOCKER SUR LINUX (UBUNTU/DEBIAN)

### Methode 1 : Installation via le script officiel (Recommandee)

```bash
# Mettre a jour les paquets
sudo apt-get update

# Installer les dependances
sudo apt-get install -y \
    ca-certificates \
    curl \
    gnupg \
    lsb-release

# Ajouter la cle GPG officielle de Docker
sudo mkdir -p /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg

# Ajouter le repository Docker
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# Installer Docker Engine
sudo apt-get update
sudo apt-get install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

### Methode 2 : Script automatique

```bash
# Telecharger et executer le script officiel
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh

# Nettoyer
rm get-docker.sh
```

### Configuration post-installation

```bash
# Ajouter votre utilisateur au groupe docker (pour ne pas utiliser sudo)
sudo usermod -aG docker $USER

# Activer Docker au demarrage
sudo systemctl enable docker
sudo systemctl start docker

# Deconnectez-vous et reconnectez-vous pour que les changements prennent effet
# Ou executez :
newgrp docker
```

### Verification

```bash
# Verifier la version
docker --version

# Tester Docker
docker run hello-world

# Verifier le service
sudo systemctl status docker
```

---

## 3. INSTALLATION DOCKER DESKTOP SUR macOS

### Pre-requis

- macOS 11 ou superieur
- Processeur Apple Silicon (M1/M2) ou Intel
- Au moins 4 Go de RAM

### Etape 1 : Telecharger Docker Desktop

1. Allez sur : https://www.docker.com/products/docker-desktop
2. Choisissez votre architecture :
   - **"Download for Mac with Apple Silicon"** (M1/M2/M3)
   - **"Download for Mac with Intel chip"** (Intel)

### Etape 2 : Installation

1. Ouvrez le fichier `Docker.dmg` telecharge
2. Glissez l'icone Docker dans le dossier Applications
3. Lancez Docker depuis le dossier Applications
4. Suivez l'assistant de configuration

### Etape 3 : Autorisation

macOS peut demander des autorisations :
- Autoriser Docker a acceder au reseau
- Entrer votre mot de passe administrateur

### Etape 4 : Verification

Ouvrez le Terminal et executez :

```bash
# Verifier la version
docker --version

# Tester Docker
docker run hello-world
```

### Configuration additionnelle (optionnelle)

```bash
# Installer Docker Compose (si pas inclus)
brew install docker-compose

# Verifier Docker Compose
docker compose version
```

---

## 4. VERIFICATION DE L'INSTALLATION

### Tests complets

```bash
# Test 1 : Version Docker
docker --version
# Sortie attendue : Docker version 24.x.x

# Test 2 : Version Docker Compose
docker compose version
# Sortie attendue : Docker Compose version v2.x.x

# Test 3 : Informations systeme
docker info

# Test 4 : Hello World
docker run hello-world

# Test 5 : Conteneur interactif
docker run -it ubuntu bash
# Tapez 'exit' pour sortir

# Test 6 : Lister les images
docker images

# Test 7 : Lister les conteneurs
docker ps -a
```

### Nettoyage apres tests

```bash
# Supprimer tous les conteneurs arretes
docker container prune -f

# Supprimer toutes les images non utilisees
docker image prune -a -f
```

---

# PARTIE 3 : CONTENEURISATION DE L'API REST {#partie3}

## 1. CREATION DU DOCKERFILE

Le **Dockerfile** est la recette pour construire votre image Docker.

### Dockerfile pour l'API REST

Creez un fichier `Dockerfile` a la racine du projet :

```dockerfile
# Etape 1 : Utiliser une image de base officielle Node.js
# Alpine est une version legere de Linux (5 Mo vs 900 Mo)
FROM node:18-alpine

# Etape 2 : Definir le repertoire de travail dans le conteneur
WORKDIR /app

# Etape 3 : Copier les fichiers package.json et package-lock.json
# On copie d'abord ces fichiers pour optimiser le cache Docker
COPY package*.json ./

# Etape 4 : Installer les dependances
# --production installe uniquement les dependances de production (pas devDependencies)
RUN npm install --production

# Etape 5 : Copier tout le code source
COPY . .

# Etape 6 : Exposer le port sur lequel l'application ecoute
EXPOSE 3000

# Etape 7 : Definir la commande par defaut pour demarrer l'application
CMD ["node", "app.js"]
```

### Explication detaillee de chaque instruction

```dockerfile
# FROM : Image de base
# ├─ node:18-alpine = Node.js version 18 sur Alpine Linux
# ├─ Alpine = Distribution Linux ultra-legere (~5 Mo)
# └─ Alternative : node:18 (Ubuntu, ~900 Mo)

# WORKDIR : Repertoire de travail
# ├─ Tous les fichiers seront dans /app
# └─ Equivalent a 'cd /app'

# COPY package*.json : Copie des fichiers de dependances
# ├─ package.json = Liste des dependances
# ├─ package-lock.json = Versions exactes
# └─ ./ = Copier dans /app (WORKDIR actuel)

# RUN npm install : Installation des dependances
# ├─ Execute au moment du BUILD (une seule fois)
# └─ --production = Ignore devDependencies

# COPY . . : Copie du code source
# ├─ Premier . = Racine du projet (source)
# └─ Second . = /app dans le conteneur (destination)

# EXPOSE 3000 : Documentation du port
# ├─ Informe que l'app ecoute sur le port 3000
# └─ N'ouvre PAS le port automatiquement

# CMD : Commande de demarrage
# ├─ Execute au moment du RUN (a chaque demarrage)
# └─ ["node", "app.js"] = Demarre l'application
```

---

## 2. CREATION DU .dockerignore

Le fichier `.dockerignore` indique a Docker quels fichiers **ne pas copier** dans l'image.

### Contenu du .dockerignore

Creez un fichier `.dockerignore` a la racine du projet :

```
# Dependencies
node_modules
npm-debug.log

# Environment variables
.env
.env.local
.env.*.local

# IDE and editors
.vscode
.idea
*.swp
*.swo
*~

# OS files
.DS_Store
Thumbs.db

# Git
.git
.gitignore

# Documentation
*.md
docs/

# Tests
tests/
*.test.js

# Docker files
Dockerfile
.dockerignore
docker-compose.yml

# Logs
logs/
*.log
```

### Pourquoi utiliser .dockerignore ?

✅ **Taille reduite** : Exclut node_modules (sera reinstalle dans le conteneur)
✅ **Securite** : Exclut .env et fichiers sensibles
✅ **Performance** : Build plus rapide
✅ **Proprete** : Evite les fichiers inutiles

---

## 3. CONSTRUCTION DE L'IMAGE DOCKER

### Commande de build

```bash
# Syntaxe de base
docker build -t nom-image:tag .

# Pour notre API REST
docker build -t api-rest-nodejs:1.0 .
```

### Explication de la commande

```
docker build
├─ -t api-rest-nodejs:1.0
│  ├─ -t = Tag (nom de l'image)
│  ├─ api-rest-nodejs = Nom de l'image
│  └─ :1.0 = Version (tag)
└─ . = Contexte de build (repertoire actuel)
```

### Execution du build

```bash
# Se placer dans le repertoire du projet
cd c:\Users\hbrinsi\Desktop\node-js

# Construire l'image
docker build -t api-rest-nodejs:1.0 .
```

### Sortie attendue

```
[+] Building 45.2s (10/10) FINISHED
 => [internal] load build definition from Dockerfile
 => => transferring dockerfile: 600B
 => [internal] load .dockerignore
 => [internal] load metadata for docker.io/library/node:18-alpine
 => [1/5] FROM docker.io/library/node:18-alpine
 => [internal] load build context
 => => transferring context: 12.45kB
 => [2/5] WORKDIR /app
 => [3/5] COPY package*.json ./
 => [4/5] RUN npm install --production
 => [5/5] COPY . .
 => exporting to image
 => => exporting layers
 => => writing image sha256:abc123...
 => => naming to docker.io/library/api-rest-nodejs:1.0
```

### Verification de l'image

```bash
# Lister toutes les images
docker images

# Sortie attendue :
# REPOSITORY          TAG       IMAGE ID       CREATED          SIZE
# api-rest-nodejs     1.0       abc123def456   2 minutes ago    150MB
# node                18-alpine xyz789abc123   2 weeks ago      120MB
```

### Informations detaillees sur l'image

```bash
# Voir les details de l'image
docker inspect api-rest-nodejs:1.0

# Voir l'historique des couches (layers)
docker history api-rest-nodejs:1.0
```

---

## 4. EXECUTION DU CONTENEUR

### Commande de base

```bash
# Syntaxe complete
docker run [OPTIONS] IMAGE [COMMAND]

# Pour notre API REST
docker run -d -p 3000:3000 --name api-container api-rest-nodejs:1.0
```

### Explication des options

```
docker run
├─ -d = Detached mode (arriere-plan)
├─ -p 3000:3000 = Port mapping
│  ├─ Premier 3000 = Port sur l'hote (votre PC)
│  └─ Second 3000 = Port dans le conteneur
├─ --name api-container = Nom du conteneur
└─ api-rest-nodejs:1.0 = Image a utiliser
```

### Schema du port mapping

```
┌─────────────────────────────────────────┐
│         VOTRE ORDINATEUR                │
│                                     │
│  Navigateur → localhost:3000            │
│                    ↓                    │
│         ┌──────────────────────┐       │
│         │   CONTENEUR DOCKER   │       │
│         │                      │       │
│         │  Port 3000:3000      │       │
│         │        ↓             │       │
│         │   app.js (port 3000) │       │
│         └──────────────────────┘       │
└─────────────────────────────────────────┘
```

### Execution du conteneur

```bash
# Demarrer le conteneur
docker run -d -p 3000:3000 --name api-container api-rest-nodejs:1.0

# Sortie : ID du conteneur
# abc123def456789...
```

### Verifications

```bash
# Voir les conteneurs en cours d'execution
docker ps

# Sortie :
# CONTAINER ID   IMAGE                  COMMAND        CREATED          STATUS          PORTS                    NAMES
# abc123def456   api-rest-nodejs:1.0    "node app.js"  10 seconds ago   Up 9 seconds    0.0.0.0:3000->3000/tcp   api-container

# Voir tous les conteneurs (actifs et arretes)
docker ps -a

# Voir les logs du conteneur
docker logs api-container

# Suivre les logs en temps reel
docker logs -f api-container
```

### Commandes de gestion du conteneur

```bash
# Arreter le conteneur
docker stop api-container

# Demarrer un conteneur arrete
docker start api-container

# Redemarrer le conteneur
docker restart api-container

# Supprimer le conteneur (il doit etre arrete)
docker rm api-container

# Forcer la suppression d'un conteneur en cours
docker rm -f api-container
```

---

## 5. TESTS DE L'APPLICATION CONTENEURISEE

### Test 1 : Verification du serveur

Ouvrez votre navigateur et allez a :
```
http://localhost:3000
```

Vous devriez voir votre page d'accueil.

### Test 2 : Test des routes API

```bash
# Windows PowerShell
Invoke-RestMethod -Uri http://localhost:3000/api/equipes -Method GET

# Linux/Mac/Git Bash
curl http://localhost:3000/api/equipes
```

### Test 3 : Routes Market

```bash
# Windows PowerShell
Invoke-RestMethod -Uri http://localhost:3000/api/market -Method GET

# Linux/Mac
curl http://localhost:3000/api/market
```

### Test 4 : Acces interactif au conteneur

```bash
# Entrer dans le conteneur (ligne de commande)
docker exec -it api-container sh

# Une fois dans le conteneur, vous pouvez :
ls -la                  # Lister les fichiers
cat app.js             # Voir le contenu d'un fichier
ps aux                 # Voir les processus
node --version         # Verifier la version de Node
exit                   # Sortir du conteneur
```

### Test 5 : Verification des logs

```bash
# Voir les 50 dernieres lignes de logs
docker logs --tail 50 api-container

# Suivre les logs en temps reel
docker logs -f api-container

# Logs avec timestamps
docker logs -t api-container
```

### Test 6 : Stats du conteneur

```bash
# Voir les statistiques en temps reel (CPU, RAM, etc.)
docker stats api-container

# Sortie :
# CONTAINER ID   NAME            CPU %   MEM USAGE / LIMIT   MEM %   NET I/O
# abc123def456   api-container   0.50%   45MiB / 8GiB        0.55%   1.2kB / 850B
```

### Test 7 : Inspection du conteneur

```bash
# Voir toutes les informations du conteneur
docker inspect api-container

# Voir uniquement l'adresse IP du conteneur
docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' api-container
```

### Troubleshooting

#### Probleme : Le conteneur ne demarre pas

```bash
# Voir les logs d'erreur
docker logs api-container

# Solutions courantes :
# 1. Port deja utilise : changer le port (-p 3001:3000)
# 2. Erreur dans app.js : verifier les logs
# 3. Dependencies manquantes : rebuild l'image
```

#### Probleme : Cannot connect to port 3000

```bash
# Verifier que le conteneur ecoute bien sur 3000
docker exec api-container netstat -tlnp

# Verifier le port mapping
docker port api-container
```

#### Probleme : Changes not reflected

```bash
# Reconstruire l'image apres modification du code
docker build -t api-rest-nodejs:1.0 .

# Supprimer l'ancien conteneur
docker rm -f api-container

# Recreer le conteneur
docker run -d -p 3000:3000 --name api-container api-rest-nodejs:1.0
```

---

## 6. VOLUMES DOCKER (OPTIONNEL)

### Pourquoi utiliser des volumes ?

Les **volumes** permettent de persister les donnees entre les redemarrages du conteneur.

### Creer un conteneur avec volume

```bash
# Syntaxe avec volume
docker run -d \
  -p 3000:3000 \
  --name api-container \
  -v $(pwd)/data:/app/data \
  api-rest-nodejs:1.0

# Explication :
# -v $(pwd)/data:/app/data
# ├─ $(pwd)/data = Dossier sur votre PC
# └─ /app/data = Dossier dans le conteneur
```

### Schema des volumes

```
┌─────────────────────────────────────┐
│      VOTRE ORDINATEUR               │
│                                     │
│  c:\...\node-js\data\               │
│  ├─ equipes.json                    │
│  └─ marketData.json                 │
│            ↕ SYNCHRONISE            │
│  ┌─────────────────────────┐       │
│  │   CONTENEUR DOCKER      │       │
│  │                         │       │
│  │  /app/data/             │       │
│  │  ├─ equipes.json        │       │
│  │  └─ marketData.json     │       │
│  └─────────────────────────┘       │
└─────────────────────────────────────┘
```

### Avantages des volumes

✅ **Persistence** : Les donnees survivent a la suppression du conteneur
✅ **Partage** : Plusieurs conteneurs peuvent partager le meme volume
✅ **Backup** : Facile de sauvegarder les donnees
✅ **Development** : Modification du code en temps reel

---

Voila pour la PARTIE 3 ! Dans la prochaine partie, nous verrons Docker Compose pour orchestrer plusieurs conteneurs.

Voulez-vous que je continue avec la PARTIE 4 (Docker Compose) ?

---

# PARTIE 4 : DOCKER COMPOSE {#partie4}

## 1. QU'EST-CE QUE DOCKER COMPOSE ?

### Definition

**Docker Compose** est un outil pour definir et executer des applications Docker multi-conteneurs avec un seul fichier de configuration YAML.

### Pourquoi Docker Compose ?

Sans Docker Compose :
```bash
# Demarrer le conteneur API
docker run -d -p 3000:3000 --name api api-rest-nodejs:1.0

# Demarrer MongoDB
docker run -d -p 27017:27017 --name mongo mongo:latest

# Demarrer Redis
docker run -d -p 6379:6379 --name redis redis:latest

# Creer un reseau
docker network create app-network

# Reconnecter tous les conteneurs au reseau
# ... beaucoup de commandes !
```

Avec Docker Compose :
```bash
# Une seule commande pour tout demarrer
docker compose up -d
```

---

## 2. CREATION DU FICHIER docker-compose.yml

### Version simple (API seule)

Creez un fichier `docker-compose.yml` a la racine du projet :

```yaml
version: '3.8'

services:
  # Service API REST
  api:
    build:
      context: .
      dockerfile: Dockerfile
    container_name: api-rest-container
    ports:
      - "3000:3000"
    volumes:
      - ./data:/app/data
    environment:
      - NODE_ENV=production
      - PORT=3000
    restart: unless-stopped
```

### Explication ligne par ligne

```yaml
# Version du format Docker Compose
version: '3.8'

# Definition des services (conteneurs)
services:

  # Nom du service
  api:
    # Construction de l'image
    build:
      context: .              # Repertoire contenant le Dockerfile
      dockerfile: Dockerfile   # Nom du Dockerfile

    # Nom personnalise du conteneur
    container_name: api-rest-container

    # Mapping des ports (hote:conteneur)
    ports:
      - "3000:3000"

    # Montage de volumes (hote:conteneur)
    volumes:
      - ./data:/app/data

    # Variables d'environnement
    environment:
      - NODE_ENV=production
      - PORT=3000

    # Politique de redemarrage
    restart: unless-stopped
    # Options : no, always, on-failure, unless-stopped
```

---

## 3. VERSION COMPLETE AVEC MONGODB

### Architecture multi-conteneurs

```
┌────────────────────────────────────────────┐
│      APPLICATION COMPLETE                  │
│                                        │
│  ┌──────────────┐  ┌──────────────┐    │
│  │  Conteneur   │  │  Conteneur   │    │
│  │   Node.js    │◄─┤   MongoDB    │    │
│  │   (API)      │  │   (DB)       │    │
│  └──────────────┘  └──────────────┘    │
│         ↑                               │
│  ┌──────────────┐                        │
│  │  Conteneur   │                        │
│  │    Nginx     │                        │
│  │ (Reverse     │                        │
│  │  Proxy)      │                        │
│  └──────────────┘                        │
└────────────────────────────────────────────┘
```

### Fichier docker-compose.yml complet

```yaml
version: '3.8'

services:
  # Service API REST Node.js
  api:
    build:
      context: .
      dockerfile: Dockerfile
    container_name: api-rest-container
    ports:
      - "3000:3000"
    volumes:
      - ./data:/app/data
      - ./logs:/app/logs
    environment:
      - NODE_ENV=production
      - PORT=3000
      - MONGODB_URI=mongodb://mongo:27017/api-rest-db
    depends_on:
      - mongo
    networks:
      - app-network
    restart: unless-stopped
    healthcheck:
      test: ["CMD", "wget", "--quiet", "--tries=1", "--spider", "http://localhost:3000"]
      interval: 30s
      timeout: 10s
      retries: 3

  # Service MongoDB
  mongo:
    image: mongo:7
    container_name: mongodb-container
    ports:
      - "27017:27017"
    volumes:
      - mongo-data:/data/db
      - mongo-config:/data/configdb
    environment:
      - MONGO_INITDB_ROOT_USERNAME=admin
      - MONGO_INITDB_ROOT_PASSWORD=secretpassword
      - MONGO_INITDB_DATABASE=api-rest-db
    networks:
      - app-network
    restart: unless-stopped
    healthcheck:
      test: ["CMD", "mongosh", "--eval", "db.adminCommand('ping')"]
      interval: 30s
      timeout: 10s
      retries: 3

  # Service Mongo Express (Interface Web pour MongoDB - OPTIONNEL)
  mongo-express:
    image: mongo-express:latest
    container_name: mongo-express-container
    ports:
      - "8081:8081"
    environment:
      - ME_CONFIG_MONGODB_ADMINUSERNAME=admin
      - ME_CONFIG_MONGODB_ADMINPASSWORD=secretpassword
      - ME_CONFIG_MONGODB_URL=mongodb://admin:secretpassword@mongo:27017/
      - ME_CONFIG_BASICAUTH_USERNAME=admin
      - ME_CONFIG_BASICAUTH_PASSWORD=admin123
    depends_on:
      - mongo
    networks:
      - app-network
    restart: unless-stopped

# Definition des volumes persistants
volumes:
  mongo-data:
    driver: local
  mongo-config:
    driver: local

# Definition du reseau
networks:
  app-network:
    driver: bridge
```

---

## 4. COMMANDES DOCKER COMPOSE

### Commandes principales

```bash
# Demarrer tous les services en arriere-plan
docker compose up -d

# Demarrer et voir les logs
docker compose up

# Arreter tous les services
docker compose down

# Arreter et supprimer les volumes
docker compose down -v

# Voir les services en cours
docker compose ps

# Voir les logs de tous les services
docker compose logs

# Voir les logs d'un service specifique
docker compose logs api

# Suivre les logs en temps reel
docker compose logs -f api

# Redemarrer un service
docker compose restart api

# Reconstruire les images
docker compose build

# Reconstruire et redemarrer
docker compose up -d --build

# Executer une commande dans un service
docker compose exec api sh

# Voir les statistiques des ressources
docker compose stats
```

---

## 5. UTILISATION PRATIQUE

### Etape 1 : Preparation

```bash
# Se placer dans le repertoire du projet
cd c:\Users\hbrinsi\Desktop\node-js

# Verifier que les fichiers existent
ls
# Vous devriez voir : Dockerfile, docker-compose.yml, app.js, etc.
```

### Etape 2 : Demarrage

```bash
# Demarrer tous les services
docker compose up -d

# Sortie attendue :
# [+] Running 4/4
#  ✔ Network node-js_app-network      Created
#  ✔ Container mongodb-container      Started
#  ✔ Container api-rest-container     Started
#  ✔ Container mongo-express-container Started
```

### Etape 3 : Verification

```bash
# Voir les conteneurs actifs
docker compose ps

# Sortie :
# NAME                    IMAGE              STATUS          PORTS
# api-rest-container      node-js-api        Up 10 seconds   0.0.0.0:3000->3000/tcp
# mongodb-container       mongo:7            Up 10 seconds   0.0.0.0:27017->27017/tcp
# mongo-express-container mongo-express      Up 10 seconds   0.0.0.0:8081->8081/tcp

# Voir les logs
docker compose logs
```

### Etape 4 : Tests

```bash
# Test API
curl http://localhost:3000/api/equipes

# Acces Mongo Express (interface web MongoDB)
# Ouvrez votre navigateur : http://localhost:8081
# Username : admin
# Password : admin123
```

### Etape 5 : Arret

```bash
# Arreter tous les services (garde les donnees)
docker compose down

# Arreter et supprimer les donnees
docker compose down -v
```

---

## 6. VARIABLES D'ENVIRONNEMENT AVEC .env

### Creer un fichier .env

Pour eviter d'exposer les mots de passe dans docker-compose.yml :

```env
# .env
NODE_ENV=production
PORT=3000

# MongoDB
MONGO_INITDB_ROOT_USERNAME=admin
MONGO_INITDB_ROOT_PASSWORD=SuperSecretPassword123!
MONGO_INITDB_DATABASE=api-rest-db

# Mongo Express
ME_CONFIG_BASICAUTH_USERNAME=admin
ME_CONFIG_BASICAUTH_PASSWORD=AdminPassword456!
```

### Utiliser .env dans docker-compose.yml

```yaml
version: '3.8'

services:
  api:
    build: .
    ports:
      - "${PORT}:3000"
    environment:
      - NODE_ENV=${NODE_ENV}
      - MONGODB_URI=mongodb://${MONGO_INITDB_ROOT_USERNAME}:${MONGO_INITDB_ROOT_PASSWORD}@mongo:27017/${MONGO_INITDB_DATABASE}
    depends_on:
      - mongo

  mongo:
    image: mongo:7
    environment:
      - MONGO_INITDB_ROOT_USERNAME=${MONGO_INITDB_ROOT_USERNAME}
      - MONGO_INITDB_ROOT_PASSWORD=${MONGO_INITDB_ROOT_PASSWORD}
      - MONGO_INITDB_DATABASE=${MONGO_INITDB_DATABASE}

  mongo-express:
    image: mongo-express
    ports:
      - "8081:8081"
    environment:
      - ME_CONFIG_MONGODB_ADMINUSERNAME=${MONGO_INITDB_ROOT_USERNAME}
      - ME_CONFIG_MONGODB_ADMINPASSWORD=${MONGO_INITDB_ROOT_PASSWORD}
      - ME_CONFIG_BASICAUTH_USERNAME=${ME_CONFIG_BASICAUTH_USERNAME}
      - ME_CONFIG_BASICAUTH_PASSWORD=${ME_CONFIG_BASICAUTH_PASSWORD}
```

### Securite du .env

```bash
# Ajouter .env au .gitignore
echo ".env" >> .gitignore

# Creer un fichier .env.example pour la documentation
cp .env .env.example

# Editer .env.example et remplacer les valeurs sensibles
# MONGO_INITDB_ROOT_PASSWORD=your_password_here
```

---

## 7. SCALING (MISE A L'ECHELLE)

### Lancer plusieurs instances de l'API

```bash
# Demarrer 3 instances de l'API
docker compose up -d --scale api=3

# Verifier
docker compose ps
```

### Load Balancer avec Nginx

Ajoutez Nginx au docker-compose.yml :

```yaml
services:
  # ... services existants ...

  nginx:
    image: nginx:alpine
    container_name: nginx-loadbalancer
    ports:
      - "80:80"
    volumes:
      - ./nginx.conf:/etc/nginx/nginx.conf:ro
    depends_on:
      - api
    networks:
      - app-network
    restart: unless-stopped
```

Creez `nginx.conf` :

```nginx
events {
    worker_connections 1024;
}

http {
    upstream api_backend {
        # Docker Compose cree automatiquement un DNS pour le service
        server api:3000;
    }

    server {
        listen 80;

        location / {
            proxy_pass http://api_backend;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
        }
    }
}
```

---

## 8. MONITORING AVEC DOCKER COMPOSE

### Ajout de Prometheus et Grafana (OPTIONNEL AVANCE)

```yaml
services:
  # ... services existants ...

  # Prometheus pour collecter les metriques
  prometheus:
    image: prom/prometheus:latest
    container_name: prometheus
    ports:
      - "9090:9090"
    volumes:
      - ./prometheus.yml:/etc/prometheus/prometheus.yml
      - prometheus-data:/prometheus
    networks:
      - app-network
    restart: unless-stopped

  # Grafana pour visualiser les metriques
  grafana:
    image: grafana/grafana:latest
    container_name: grafana
    ports:
      - "3001:3000"
    environment:
      - GF_SECURITY_ADMIN_PASSWORD=admin
    volumes:
      - grafana-data:/var/lib/grafana
    depends_on:
      - prometheus
    networks:
      - app-network
    restart: unless-stopped

volumes:
  prometheus-data:
  grafana-data:
```

Fichier `prometheus.yml` :

```yaml
global:
  scrape_interval: 15s

scrape_configs:
  - job_name: 'api-rest'
    static_configs:
      - targets: ['api:3000']
```

---

## 9. BEST PRACTICES DOCKER COMPOSE

### ✅ Bonnes pratiques

1. **Nommage explicite des services**
```yaml
services:
  api-backend:     # Bon : clair et descriptif
  db:              # A eviter : trop generique
```

2. **Utiliser des healthchecks**
```yaml
healthcheck:
  test: ["CMD", "curl", "-f", "http://localhost:3000/health"]
  interval: 30s
  timeout: 10s
  retries: 3
```

3. **Ordre de demarrage avec depends_on**
```yaml
api:
  depends_on:
    - mongo
    - redis
```

4. **Limiter les ressources**
```yaml
api:
  deploy:
    resources:
      limits:
        cpus: '0.5'
        memory: 512M
      reservations:
        cpus: '0.25'
        memory: 256M
```

5. **Utiliser des volumes nommes pour la persistence**
```yaml
volumes:
  - mongo-data:/data/db  # Bon : volume nomme
  # vs
  - ./data:/data/db      # Pour development seulement
```

---

# PARTIE 5 : KUBERNETES (K8S) {#partie5}

## 1. INTRODUCTION A KUBERNETES

### Qu'est-ce que Kubernetes ?

**Kubernetes** (K8s) est une plateforme open-source d'orchestration de conteneurs qui automatise le deploiement, la mise a l'echelle et la gestion des applications conteneurisees.

### Kubernetes vs Docker Compose

| Critere | Docker Compose | Kubernetes |
|---------|----------------|------------|
| **Usage** | Development local | Production a grande echelle |
| **Complexite** | Simple | Plus complexe |
| **Scaling** | Manuel | Automatique |
| **Auto-healing** | Non | Oui |
| **Load Balancing** | Basique | Avance |
| **Environnements** | Mono-machine | Multi-machines (cluster) |

### Architecture Kubernetes

```
┌────────────────────────────────────────────────────────────┐
│                    KUBERNETES CLUSTER                      │
│                                                            │
│  ┌──────────────────────────────────────────────────────┐ │
│  │              CONTROL PLANE (Master)                   │ │
│  │  ┌──────────┐  ┌──────────┐  ┌──────────┐           │ │
│  │  │API Server│  │Scheduler │  │Controller│           │ │
│  │  └──────────┘  └──────────┘  └──────────┘           │ │
│  │  ┌─────────────────────────────────────┐             │ │
│  │  │           etcd (Database)           │             │ │
│  │  └─────────────────────────────────────┘             │ │
│  └──────────────────────────────────────────────────────┘ │
│                         ↓                                  │
│  ┌──────────────────────────────────────────────────────┐ │
│  │                    WORKER NODES                       │ │
│  │                                                       │ │
│  │  Node 1              Node 2              Node 3      │ │
│  │  ┌─────────┐        ┌─────────┐        ┌─────────┐  │ │
│  │  │ Pod 1   │        │ Pod 2   │        │ Pod 3   │  │ │
│  │  │┌───────┐│        │┌───────┐│        │┌───────┐│  │ │
│  │  ││  API  ││        ││  API  ││        ││  DB   ││  │ │
│  │  │└───────┘│        │└───────┘│        │└───────┘│  │ │
│  │  └─────────┘        └─────────┘        └─────────┘  │ │
│  │  ┌─────────┐        ┌─────────┐        ┌─────────┐  │ │
│  │  │ Kubelet │        │ Kubelet │        │ Kubelet │  │ │
│  │  └─────────┘        └─────────┘        └─────────┘  │ │
│  └──────────────────────────────────────────────────────┘ │
└────────────────────────────────────────────────────────────┘
```

---

## 2. CONCEPTS FONDAMENTAUX DE KUBERNETES

### 1. Pod

**Definition** : Unite de base de deploiement. Un Pod contient un ou plusieurs conteneurs.

```
┌─────────────────────┐
│       POD           │
│  ┌──────────────┐   │
│  │  Conteneur 1 │   │
│  │  (API)       │   │
│  └──────────────┘   │
│  ┌──────────────┐   │
│  │  Conteneur 2 │   │
│  │  (Sidecar)   │   │
│  └──────────────┘   │
│  IP: 10.1.1.5       │
└─────────────────────┘
```

### 2. Deployment

**Definition** : Gere le deploiement et la mise a jour des Pods.

```
┌─────────────────────────────────┐
│        DEPLOYMENT               │
│   Replicas: 3                   │
│   ┌─────┐  ┌─────┐  ┌─────┐    │
│   │ Pod │  │ Pod │  │ Pod │    │
│   │  1  │  │  2  │  │  3  │    │
│   └─────┘  └─────┘  └─────┘    │
└─────────────────────────────────┘
```

### 3. Service

**Definition** : Expose les Pods au reseau (load balancer interne).

```
┌──────────────────────────────────┐
│          SERVICE                 │
│       (Load Balancer)            │
│          Port: 3000              │
└──────────────────────────────────┘
              ↓
     ┌────────┼────────┐
     ↓        ↓        ↓
  ┌─────┐ ┌─────┐ ┌─────┐
  │Pod 1│ │Pod 2│ │Pod 3│
  └─────┘ └─────┘ └─────┘
```

### 4. Namespace

**Definition** : Isolation logique des ressources.

```
┌────────────────────────────────┐
│     KUBERNETES CLUSTER         │
│                                │
│  ┌──────────────────────────┐ │
│  │  Namespace: production   │ │
│  │  - API Pods              │ │
│  │  - DB Pods               │ │
│  └──────────────────────────┘ │
│                                │
│  ┌──────────────────────────┐ │
│  │  Namespace: development  │ │
│  │  - API Pods (test)       │ │
│  └──────────────────────────┘ │
└────────────────────────────────┘
```

### 5. ConfigMap et Secret

```
┌─────────────────────────────────┐
│        ConfigMap                │
│  (Configuration non-sensible)   │
│  - API_URL                      │
│  - LOG_LEVEL                    │
│  - DB_PASSWORD                  │
└─────────────────────────────────┘
                ↓
┌─────────────────────────────────┐
│          Secret                 │
│  (Configuration sensible)       │
│  - DB_PASSWORD (encodé)         │
│  - API_KEY (encodé)             │
└─────────────────────────────────┘
                ↓
┌─────────────────────────────────┐
│            Pod                  │
│  Utilise ConfigMap + Secret     │
└─────────────────────────────────┘
```

---

## 3. INSTALLATION DE MINIKUBE

### Qu'est-ce que Minikube ?

**Minikube** est un cluster Kubernetes local pour le developpement et l'apprentissage.

### Installation sur Windows

```powershell
# Methode 1 : Avec Chocolatey
choco install minikube

# Methode 2 : Telechargement manuel
# 1. Telecharger depuis : https://minikube.sigs.k8s.io/docs/start/
# 2. Ajouter au PATH

# Verifier l'installation
minikube version
```

### Installation sur Linux

```bash
# Telecharger Minikube
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64

# Installer
sudo install minikube-linux-amd64 /usr/local/bin/minikube

# Verifier
minikube version
```

### Installation sur macOS

```bash
# Avec Homebrew
brew install minikube

# Verifier
minikube version
```

### Demarrer Minikube

```bash
# Demarrer Minikube avec Docker comme driver
minikube start --driver=docker

# Sortie attendue :
# 😄  minikube v1.32.0 on Windows 11
# ✨  Using the docker driver based on user configuration
# 👍  Starting control plane node minikube in cluster minikube
# 🚜  Pulling base image ...
# 🔥  Creating docker container (CPUs=2, Memory=4000MB) ...
# 🐳  Preparing Kubernetes v1.28.3 on Docker 24.0.7 ...
# 🔗  Configuring bridge CNI (Container Networking Interface) ...
# 🔎  Verifying Kubernetes components...
# 🌟  Enabled addons: storage-provisioner, default-storageclass
# 🏄  Done! kubectl is now configured to use "minikube" cluster

# Verifier le statut
minikube status

# Sortie :
# minikube
# type: Control Plane
# host: Running
# kubelet: Running
# apiserver: Running
# kubeconfig: Configured
```

### Commandes Minikube utiles

```bash
# Arreter Minikube
minikube stop

# Supprimer le cluster
minikube delete

# Acceder au dashboard Kubernetes (interface web)
minikube dashboard

# Voir les addons disponibles
minikube addons list

# Activer un addon (exemple : metrics-server)
minikube addons enable metrics-server

# SSH dans le node Minikube
minikube ssh

# Obtenir l'IP de Minikube
minikube ip
```

---

## 4. DEPLOIEMENT DE L'API SUR KUBERNETES

### Workflow de deploiement

```
Dockerfile → Docker Image → Push to Registry → K8s Deployment
```

### Etape 1 : Construire et tagger l'image

```bash
# Se placer dans le projet
cd c:\Users\hbrinsi\Desktop\node-js

# Construire l'image
docker build -t api-rest-nodejs:1.0 .

# Tagger pour utilisation avec Minikube
# Option 1 : Utiliser le Docker daemon de Minikube
eval $(minikube docker-env)
docker build -t api-rest-nodejs:1.0 .

# Option 2 : Charger l'image dans Minikube
minikube image load api-rest-nodejs:1.0
```

### Etape 2 : Creer le fichier de Deployment

Creez `k8s/deployment.yaml` :

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: api-rest-deployment
  labels:
    app: api-rest
spec:
  replicas: 3  # Nombre de Pods
  selector:
    matchLabels:
      app: api-rest
  template:
    metadata:
      labels:
        app: api-rest
    spec:
      containers:
      - name: api-rest-container
        image: api-rest-nodejs:1.0
        imagePullPolicy: Never  # Ne pas telecharger depuis un registry
        ports:
        - containerPort: 3000
        env:
        - name: NODE_ENV
          value: "production"
        - name: PORT
          value: "3000"
        resources:
          requests:
            memory: "128Mi"
            cpu: "100m"
          limits:
            memory: "256Mi"
            cpu: "200m"
        livenessProbe:
          httpGet:
            path: /
            port: 3000
          initialDelaySeconds: 10
          periodSeconds: 10
        readinessProbe:
          httpGet:
            path: /
            port: 3000
          initialDelaySeconds: 5
          periodSeconds: 5
```

### Etape 3 : Creer le fichier de Service

Creez `k8s/service.yaml` :

```yaml
apiVersion: v1
kind: Service
metadata:
  name: api-rest-service
spec:
  type: NodePort  # Pour acceder depuis l'exterieur avec Minikube
  selector:
    app: api-rest
  ports:
  - protocol: TCP
    port: 3000        # Port du Service
    targetPort: 3000  # Port du conteneur
    nodePort: 30000   # Port expose sur le node (30000-32767)
```

### Etape 4 : Deployer sur Kubernetes

```bash
# Creer le namespace (optionnel)
kubectl create namespace api-rest-app

# Appliquer le Deployment
kubectl apply -f k8s/deployment.yaml

# Sortie :
# deployment.apps/api-rest-deployment created

# Appliquer le Service
kubectl apply -f k8s/service.yaml

# Sortie :
# service/api-rest-service created
```

### Etape 5 : Verifier le deploiement

```bash
# Voir les deployments
kubectl get deployments

# Sortie :
# NAME                  READY   UP-TO-DATE   AVAILABLE   AGE
# api-rest-deployment   3/3     3            3           1m

# Voir les pods
kubectl get pods

# Sortie :
# NAME                                   READY   STATUS    RESTARTS   AGE
# api-rest-deployment-abc123-x1y2z       1/1     Running   0          1m
# api-rest-deployment-abc123-a2b3c       1/1     Running   0          1m
# api-rest-deployment-abc123-d4e5f       1/1     Running   0          1m

# Voir les services
kubectl get services

# Sortie :
# NAME               TYPE       CLUSTER-IP      EXTERNAL-IP   PORTS          AGE
# api-rest-service   NodePort   10.96.123.45    <none>        3000:30000/TCP   1m

# Voir les details d'un pod
kubectl describe pod <pod-name>

# Voir les logs du conteneur
kubectl logs <pod-name>

# Suivre les logs en temps reel
kubectl logs -f <pod-name>
```

### Etape 6 : Acceder a l'application

```bash
# Obtenir l'URL du service
minikube service api-rest-service --url

# Sortie : http://192.168.49.2:30000

# Ou ouvrir directement dans le navigateur
minikube service api-rest-service

# Tester avec curl
curl $(minikube service api-rest-service --url)/api/equipes
```

---

## 5. MISE A L'ECHELLE

### Scaling manuel

```bash
# Augmenter le nombre de replicas a 5
kubectl scale deployment api-rest-deployment --replicas=5

# Verifier
kubectl get pods

# Sortie : Vous verrez maintenant 5 pods
```

### Auto-scaling (HPA - Horizontal Pod Autoscaler)

Creez `k8s/hpa.yaml` :

```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: api-rest-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: api-rest-deployment
  minReplicas: 2
  maxReplicas: 10
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 70  # Scale quand CPU > 70%
  - type: Resource
    resource:
      name: memory
      target:
        type: Utilization
        averageUtilization: 80  # Scale quand RAM > 80%
```

Appliquer :

```bash
# Activer metrics-server (necessaire pour HPA)
minikube addons enable metrics-server

# Appliquer HPA
kubectl apply -f k8s/hpa.yaml

# Verifier le HPA
kubectl get hpa

# Sortie :
# NAME            REFERENCE                        TARGETS   MINPODS   MAXPODS   REPLICAS
# api-rest-hpa    Deployment/api-rest-deployment   15%/70%   2         10        2
```

### Tester l'auto-scaling

```bash
# Generer du trafic (ouvrir un nouveau terminal)
while true; do curl $(minikube service api-rest-service --url)/api/equipes; done

# Observer le scaling (dans un autre terminal)
kubectl get hpa -w  # -w = watch mode

# Vous verrez les replicas augmenter automatiquement
```

---

## 6. GESTION DES CONFIGURATIONS

### ConfigMap pour les configurations

Creez `k8s/configmap.yaml` :

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: api-rest-config
data:
  NODE_ENV: "production"
  LOG_LEVEL: "info"
  API_VERSION: "v1"
  MAX_CONNECTIONS: "100"
```

### Secret pour les mots de passe

Creez `k8s/secret.yaml` :

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: api-rest-secret
type: Opaque
data:
  # Les valeurs doivent etre encodees en base64
  # echo -n 'MonMotDePasse123!' | base64
  DB_PASSWORD: TW9uTW90RGVQYXNzZTEyMyE=
  API_KEY: YWJjMTIzZGVmNDU2Z2hpNzg5
```

### Utiliser ConfigMap et Secret dans le Deployment

Modifiez `k8s/deployment.yaml` :

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: api-rest-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: api-rest
  template:
    metadata:
      labels:
        app: api-rest
    spec:
      containers:
      - name: api-rest-container
        image: api-rest-nodejs:1.0
        imagePullPolicy: Never
        ports:
        - containerPort: 3000
        env:
        # Variables depuis ConfigMap
        - name: NODE_ENV
          valueFrom:
            configMapKeyRef:
              name: api-rest-config
              key: NODE_ENV
        - name: LOG_LEVEL
          valueFrom:
            configMapKeyRef:
              name: api-rest-config
              key: LOG_LEVEL
        # Variables depuis Secret
        - name: DB_PASSWORD
          valueFrom:
            secretKeyRef:
              name: api-rest-secret
              key: DB_PASSWORD
        - name: API_KEY
          valueFrom:
            secretKeyRef:
              name: api-rest-secret
              key: API_KEY
```

Appliquer :

```bash
# Creer le ConfigMap
kubectl apply -f k8s/configmap.yaml

# Creer le Secret
kubectl apply -f k8s/secret.yaml

# Mettre a jour le Deployment
kubectl apply -f k8s/deployment.yaml

# Verifier les variables d'environnement dans un pod
kubectl exec -it <pod-name> -- env | grep NODE_ENV
```

---

## 7. VOLUMES PERSISTANTS (PERSISTENT VOLUMES)

### PersistentVolumeClaim pour les donnees

Creez `k8s/pvc.yaml` :

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: api-rest-pvc
spec:
  accessModes:
    - ReadWriteOnce  # Un seul node peut monter en lecture/ecriture
  resources:
    requests:
      storage: 1Gi
```

### Utiliser le PVC dans le Deployment

Modifiez `k8s/deployment.yaml` :

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: api-rest-deployment
spec:
  replicas: 1  # Avec ReadWriteOnce, un seul replica peut acceder au volume
  template:
    spec:
      containers:
      - name: api-rest-container
        image: api-rest-nodejs:1.0
        volumeMounts:
        - name: data-volume
          mountPath: /app/data
      volumes:
      - name: data-volume
        persistentVolumeClaim:
          claimName: api-rest-pvc
```

---

## 8. DEPLOIEMENT COMPLET AVEC MONGODB

### Architecture complete

```
┌─────────────────────────────────────────────┐
│         KUBERNETES CLUSTER                  │
│                                             │
│  ┌────────────────┐    ┌────────────────┐  │
│  │  API Service   │    │  Mongo Service │  │
│  │  (LoadBalancer)│    │  (ClusterIP)   │  │
│  └────────────────┘    └────────────────┘  │
│         ↓                       ↓           │
│  ┌────────────────┐    ┌────────────────┐  │
│  │ API Deployment │───▶│ Mongo Deploy   │  │
│  │  (3 replicas)  │    │  (1 replica)   │  │
│  └────────────────┘    └────────────────┘  │
│                               ↓             │
│                        ┌────────────────┐   │
│                        │ Mongo PVC      │   │
│                        │ (Persistent)   │   │
│                        └────────────────┘   │
└─────────────────────────────────────────────┘
```

### Fichier complet : k8s/all-in-one.yaml

```yaml
---
# ConfigMap
apiVersion: v1
kind: ConfigMap
metadata:
  name: api-config
data:
  NODE_ENV: "production"
  MONGODB_URI: "mongodb://mongo-service:27017/api-rest-db"

---
# Secret
apiVersion: v1
kind: Secret
metadata:
  name: mongo-secret
type: Opaque
data:
  MONGO_ROOT_PASSWORD: c3VwZXJzZWNyZXQ=  # supersecret en base64

---
# MongoDB PVC
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mongo-pvc
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 2Gi

---
# MongoDB Deployment
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mongo-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: mongo
  template:
    metadata:
      labels:
        app: mongo
    spec:
      containers:
      - name: mongo
        image: mongo:7
        ports:
        - containerPort: 27017
        env:
        - name: MONGO_INITDB_ROOT_PASSWORD
          valueFrom:
            secretKeyRef:
              name: mongo-secret
              key: MONGO_ROOT_PASSWORD
        volumeMounts:
        - name: mongo-storage
          mountPath: /data/db
      volumes:
      - name: mongo-storage
        persistentVolumeClaim:
          claimName: mongo-pvc

---
# MongoDB Service
apiVersion: v1
kind: Service
metadata:
  name: mongo-service
spec:
  selector:
    app: mongo
  ports:
  - protocol: TCP
    port: 27017
    targetPort: 27017
  type: ClusterIP  # Accessible uniquement dans le cluster

---
# API Deployment
apiVersion: apps/v1
kind: Deployment
metadata:
  name: api-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: api
  template:
    metadata:
      labels:
        app: api
    spec:
      containers:
      - name: api
        image: api-rest-nodejs:1.0
        imagePullPolicy: Never
        ports:
        - containerPort: 3000
        env:
        - name: NODE_ENV
          valueFrom:
            configMapKeyRef:
              name: api-config
              key: NODE_ENV
        - name: MONGODB_URI
          valueFrom:
            configMapKeyRef:
              name: api-config
              key: MONGODB_URI
        resources:
          requests:
            memory: "128Mi"
            cpu: "100m"
          limits:
            memory: "256Mi"
            cpu: "200m"

---
# API Service
apiVersion: v1
kind: Service
metadata:
  name: api-service
spec:
  type: NodePort
  selector:
    app: api
  ports:
  - protocol: TCP
    port: 3000
    targetPort: 3000
    nodePort: 30000
```

### Deployer tout en une commande

```bash
# Appliquer tous les fichiers
kubectl apply -f k8s/all-in-one.yaml

# Verifier tout
kubectl get all

# Acceder a l'API
minikube service api-service
```

---

## 9. COMMANDES KUBECTL ESSENTIELLES

### Gestion des ressources

```bash
# Creer une ressource
kubectl apply -f fichier.yaml

# Supprimer une ressource
kubectl delete -f fichier.yaml

# Lister toutes les ressources
kubectl get all

# Lister par type
kubectl get pods
kubectl get deployments
kubectl get services
kubectl get configmaps
kubectl get secrets

# Details d'une ressource
kubectl describe pod <pod-name>
kubectl describe deployment <deployment-name>

# Modifier une ressource
kubectl edit deployment <deployment-name>
```

### Debugging

```bash
# Logs d'un pod
kubectl logs <pod-name>

# Logs en temps reel
kubectl logs -f <pod-name>

# Logs d'un conteneur specifique
kubectl logs <pod-name> -c <container-name>

# Executer une commande dans un pod
kubectl exec <pod-name> -- ls /app

# Shell interactif
kubectl exec -it <pod-name> -- sh

# Port forwarding (acceder a un pod depuis votre machine)
kubectl port-forward <pod-name> 8080:3000
# Acceder via : http://localhost:8080
```

### Gestion du cluster

```bash
# Informations sur le cluster
kubectl cluster-info

# Nodes du cluster
kubectl get nodes

# Utilisation des ressources
kubectl top nodes
kubectl top pods

# Evenements
kubectl get events

# Contexte kubectl
kubectl config get-contexts
kubectl config use-context minikube
```

---

## 10. ROLLBACK ET MISE A JOUR

### Rolling Update (mise a jour progressive)

```bash
# Mettre a jour l'image du deployment
kubectl set image deployment/api-deployment api=api-rest-nodejs:2.0

# Suivre le deploiement
kubectl rollout status deployment/api-deployment

# Historique des deploiements
kubectl rollout history deployment/api-deployment
```

### Rollback (retour arriere)

```bash
# Revenir a la version precedente
kubectl rollout undo deployment/api-deployment

# Revenir a une version specifique
kubectl rollout undo deployment/api-deployment --to-revision=2

# Pausar un rollout
kubectl rollout pause deployment/api-deployment

# Reprendre un rollout
kubectl rollout resume deployment/api-deployment
```

---

## 11. INGRESS (ROUTAGE HTTP/HTTPS)

### Qu'est-ce qu'un Ingress ?

Un **Ingress** expose les routes HTTP/HTTPS vers les services.

```
Internet
    ↓
┌─────────────────────┐
│      INGRESS        │
│  (Reverse Proxy)    │
└─────────────────────┘
    ↓           ↓
Service 1   Service 2
    ↓           ↓
  Pods        Pods
```

### Activer Ingress dans Minikube

```bash
# Activer l'addon ingress
minikube addons enable ingress

# Verifier
kubectl get pods -n ingress-nginx
```

### Creer un Ingress

Creez `k8s/ingress.yaml` :

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: api-ingress
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  rules:
  - host: api.local
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: api-service
            port:
              number: 3000
```

Appliquer et tester :

```bash
# Appliquer l'Ingress
kubectl apply -f k8s/ingress.yaml

# Obtenir l'IP de Minikube
minikube ip
# Exemple : 192.168.49.2

# Ajouter a votre fichier hosts
# Windows : C:\Windows\System32\drivers\etc\hosts
# Linux/Mac : /etc/hosts
# Ajouter la ligne :
192.168.49.2 api.local

# Tester
curl http://api.local/api/equipes
```

---

Voila pour la PARTIE 5 sur Kubernetes ! Prochaine etape : les bonnes pratiques.

Voulez-vous que je continue avec la PARTIE 6 (Bonnes pratiques) ?

---

# PARTIE 6 : BONNES PRATIQUES {#partie6}

## 1. OPTIMISATION DES IMAGES DOCKER

### ❌ Mauvaise pratique

```dockerfile
# Image lourde (~900 Mo)
FROM node:18

WORKDIR /app
COPY . .
RUN npm install
CMD ["node", "app.js"]
```

### ✅ Bonne pratique : Multi-stage build

```dockerfile
# Stage 1 : Build
FROM node:18-alpine AS builder
WORKDIR /app
COPY package*.json ./
RUN npm ci --only=production

# Stage 2 : Production
FROM node:18-alpine
WORKDIR /app

# Copier uniquement les fichiers necessaires
COPY --from=builder /app/node_modules ./node_modules
COPY . .

# Creer un utilisateur non-root
RUN addgroup -g 1001 -S nodejs && \
    adduser -S nodejs -u 1001

# Changer le proprietaire
RUN chown -R nodejs:nodejs /app

# Utiliser l'utilisateur non-root
USER nodejs

EXPOSE 3000
CMD ["node", "app.js"]
```

### Comparaison des tailles

| Type d'image | Taille |
|--------------|--------|
| node:18 | ~900 Mo |
| node:18-alpine | ~120 Mo |
| Multi-stage optimise | ~150 Mo |

### Autres optimisations

```dockerfile
# 1. Utiliser .dockerignore
# Creer .dockerignore :
node_modules
*.log
.git
.env

# 2. Copier package.json en premier (cache Docker)
COPY package*.json ./
RUN npm ci --only=production
COPY . .

# 3. Minimiser les couches (layers)
# ❌ Mauvais
RUN apt-get update
RUN apt-get install -y curl
RUN apt-get clean

# ✅ Bon
RUN apt-get update && \
    apt-get install -y curl && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*
```

---

## 2. SECURITE DES CONTENEURS

### 1. Ne jamais utiliser root

```dockerfile
# ✅ Creer un utilisateur non-root
RUN addgroup -g 1001 -S nodejs && \
    adduser -S nodejs -u 1001
USER nodejs
```

### 2. Scanner les vulnerabilites

```bash
# Installer Trivy (scanner de vulnerabilites)
# Windows (avec Chocolatey)
choco install trivy

# Linux
sudo apt-get install trivy

# Scanner une image
trivy image api-rest-nodejs:1.0

# Scanner avec severite HIGH et CRITICAL uniquement
trivy image --severity HIGH,CRITICAL api-rest-nodejs:1.0
```

### 3. Utiliser des secrets de maniere securisee

```bash
# ❌ Mauvais : exposer des secrets dans le Dockerfile
ENV DB_PASSWORD=supersecret

# ✅ Bon : utiliser Docker secrets ou variables d'environnement
docker run -e DB_PASSWORD=supersecret api-rest-nodejs:1.0

# ✅ Meilleur : utiliser un fichier .env (non versionne)
docker run --env-file .env api-rest-nodejs:1.0

# ✅ Kubernetes : utiliser des Secrets
kubectl create secret generic db-secret --from-literal=password=supersecret
```

### 4. Limiter les ressources

```yaml
# Kubernetes
resources:
  limits:
    memory: "256Mi"
    cpu: "500m"
  requests:
    memory: "128Mi"
    cpu: "250m"

# Docker Compose
services:
  api:
    deploy:
      resources:
        limits:
          cpus: '0.5'
          memory: 512M
        reservations:
          cpus: '0.25'
          memory: 256M
```

### 5. Utiliser des images officielles et verifiees

```dockerfile
# ✅ Image officielle
FROM node:18-alpine

# ❌ Image non verifiee
FROM random-user/node:latest
```

### 6. Mettre a jour regulierement

```bash
# Mettre a jour l'image de base
docker pull node:18-alpine

# Reconstruire
docker build -t api-rest-nodejs:1.0 .
```

---

## 3. LOGGING ET MONITORING

### 1. Logging dans Docker

```bash
# Configurer le driver de logs
docker run -d \
  --log-driver json-file \
  --log-opt max-size=10m \
  --log-opt max-file=3 \
  api-rest-nodejs:1.0

# Voir les logs
docker logs <container-id>

# Logs avec timestamps
docker logs -t <container-id>

# Suivre les logs
docker logs -f <container-id>
```

### 2. Centraliser les logs avec ELK Stack

```yaml
# docker-compose.yml avec ELK
version: '3.8'

services:
  api:
    image: api-rest-nodejs:1.0
    logging:
      driver: "json-file"
      options:
        max-size: "10m"
        max-file: "3"

  elasticsearch:
    image: elasticsearch:8.10.0
    environment:
      - discovery.type=single-node
    ports:
      - "9200:9200"

  logstash:
    image: logstash:8.10.0
    volumes:
      - ./logstash.conf:/usr/share/logstash/pipeline/logstash.conf
    depends_on:
      - elasticsearch

  kibana:
    image: kibana:8.10.0
    ports:
      - "5601:5601"
    depends_on:
      - elasticsearch
```

### 3. Monitoring avec Prometheus et Grafana

```yaml
# docker-compose.yml
services:
  prometheus:
    image: prom/prometheus:latest
    ports:
      - "9090:9090"
    volumes:
      - ./prometheus.yml:/etc/prometheus/prometheus.yml
      - prometheus-data:/prometheus

  grafana:
    image: grafana/grafana:latest
    ports:
      - "3001:3000"
    environment:
      - GF_SECURITY_ADMIN_PASSWORD=admin
    volumes:
      - grafana-data:/var/lib/grafana
    depends_on:
      - prometheus

volumes:
  prometheus-data:
  grafana-data:
```

Fichier `prometheus.yml` :

```yaml
global:
  scrape_interval: 15s

scrape_configs:
  - job_name: 'api-rest'
    static_configs:
      - targets: ['api:3000']
```

### 4. Health Checks

```dockerfile
# Dans le Dockerfile
HEALTHCHECK --interval=30s --timeout=3s --start-period=5s --retries=3 \
  CMD node healthcheck.js || exit 1
```

Creez `healthcheck.js` :

```javascript
const http = require('http');

const options = {
  host: 'localhost',
  port: 3000,
  path: '/health',
  timeout: 2000
};

const request = http.request(options, (res) => {
  console.log(`STATUS: ${res.statusCode}`);
  if (res.statusCode == 200) {
    process.exit(0);
  } else {
    process.exit(1);
  }
});

request.on('error', (err) => {
  console.log('ERROR', err);
  process.exit(1);
});

request.end();
```

Et dans `app.js`, ajoutez :

```javascript
// Route de health check
app.get('/health', (req, res) => {
  res.status(200).json({ status: 'OK', timestamp: new Date() });
});
```

---

## 4. CI/CD AVEC DOCKER

### 1. Pipeline GitHub Actions

Creez `.github/workflows/docker.yml` :

```yaml
name: Docker CI/CD

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

jobs:
  build:
    runs-on: ubuntu-latest
    
    steps:
    - name: Checkout code
      uses: actions/checkout@v3
    
    - name: Set up Docker Buildx
      uses: docker/setup-buildx-action@v2
    
    - name: Login to Docker Hub
      uses: docker/login-action@v2
      with:
        username: ${{ secrets.DOCKER_USERNAME }}
        password: ${{ secrets.DOCKER_PASSWORD }}
    
    - name: Build and push
      uses: docker/build-push-action@v4
      with:
        context: .
        push: true
        tags: votre-username/api-rest-nodejs:latest
    
    - name: Run tests
      run: |
        docker run --rm votre-username/api-rest-nodejs:latest npm test
```

### 2. Pipeline GitLab CI

Creez `.gitlab-ci.yml` :

```yaml
stages:
  - build
  - test
  - deploy

build:
  stage: build
  image: docker:latest
  services:
    - docker:dind
  script:
    - docker build -t $CI_REGISTRY_IMAGE:$CI_COMMIT_SHA .
    - docker push $CI_REGISTRY_IMAGE:$CI_COMMIT_SHA

test:
  stage: test
  image: docker:latest
  script:
    - docker run --rm $CI_REGISTRY_IMAGE:$CI_COMMIT_SHA npm test

deploy:
  stage: deploy
  image: bitnami/kubectl:latest
  script:
    - kubectl set image deployment/api-deployment api=$CI_REGISTRY_IMAGE:$CI_COMMIT_SHA
  only:
    - main
```

---

## 5. BACKUP ET RESTAURATION

### 1. Backup des volumes Docker

```bash
# Creer un backup d'un volume
docker run --rm \
  -v nom-volume:/data \
  -v $(pwd):/backup \
  alpine tar czf /backup/backup-$(date +%Y%m%d).tar.gz -C /data .

# Restaurer un backup
docker run --rm \
  -v nom-volume:/data \
  -v $(pwd):/backup \
  alpine tar xzf /backup/backup-20231201.tar.gz -C /data
```

### 2. Backup MongoDB dans Kubernetes

```bash
# Creer un backup
kubectl exec -it <mongo-pod-name> -- mongodump --out /tmp/backup

# Copier le backup localement
kubectl cp <mongo-pod-name>:/tmp/backup ./mongo-backup

# Restaurer
kubectl cp ./mongo-backup <mongo-pod-name>:/tmp/restore
kubectl exec -it <mongo-pod-name> -- mongorestore /tmp/restore
```

### 3. Backup avec CronJob Kubernetes

Creez `k8s/backup-cronjob.yaml` :

```yaml
apiVersion: batch/v1
kind: CronJob
metadata:
  name: mongo-backup
spec:
  schedule: "0 2 * * *"  # Tous les jours a 2h du matin
  jobTemplate:
    spec:
      template:
        spec:
          containers:
          - name: backup
            image: mongo:7
            command:
            - /bin/sh
            - -c
            - |
              mongodump --host mongo-service --out /backup/$(date +%Y%m%d)
              # Uploader vers S3, Google Cloud Storage, etc.
          restartPolicy: OnFailure
```

---

## 6. PERFORMANCE ET OPTIMISATION

### 1. Utiliser le cache Docker intelligemment

```dockerfile
# ✅ Bon ordre (cache optimal)
FROM node:18-alpine
WORKDIR /app

# 1. Copier package.json d'abord (change rarement)
COPY package*.json ./
RUN npm ci --only=production

# 2. Copier le code ensuite (change souvent)
COPY . .

# 3. Minimiser les couches (layers)
RUN apk add --no-cache curl
```

### 2. Minimiser la taille de l'image

```dockerfile
# Multi-stage build pour exclure les outils de build
FROM node:18-alpine AS builder
WORKDIR /app
COPY package*.json ./
RUN npm ci --only=production

FROM node:18-alpine
WORKDIR /app
COPY --from=builder /app/node_modules ./node_modules
COPY . .

# Supprimer les fichiers inutiles
RUN rm -rf tests/ docs/ *.md

# Creer un utilisateur non-root
RUN addgroup -g 1001 -S nodejs && \
    adduser -S nodejs -u 1001

# Changer le proprietaire
RUN chown -R nodejs:nodejs /app

# Utiliser l'utilisateur non-root
USER nodejs

EXPOSE 3000
CMD ["node", "app.js"]
```

### 3. Utiliser BuildKit

```bash
# Activer BuildKit (plus rapide)
export DOCKER_BUILDKIT=1
docker build -t api-rest-nodejs:1.0 .

# Ou de maniere permanente dans ~/.docker/config.json
{
  "features": {
    "buildkit": true
  }
}
```

### 4. Optimisation Kubernetes

```yaml
# Utiliser les readiness et liveness probes
livenessProbe:
  httpGet:
    path: /health
    port: 3000
  initialDelaySeconds: 10
  periodSeconds: 10

readinessProbe:
  httpGet:
    path: /ready
    port: 3000
  initialDelaySeconds: 5
  periodSeconds: 5

# Definir les ressources appropriees
resources:
  requests:
    memory: "128Mi"
    cpu: "100m"
  limits:
    memory: "256Mi"
    cpu: "200m"

# Utiliser Pod Disruption Budget
apiVersion: policy/v1
kind: PodDisruptionBudget
metadata:
  name: api-pdb
spec:
  minAvailable: 2
  selector:
    matchLabels:
      app: api
```

---

## 7. DOCUMENTATION

### 1. Documenter le Dockerfile

```dockerfile
# Image de base optimisee pour la production
# Alpine Linux pour reduire la taille (~120 Mo vs ~900 Mo)
FROM node:18-alpine

# Metadata de l'image
LABEL maintainer="votre-email@example.com"
LABEL version="1.0"
LABEL description="API REST pour la gestion d'equipes de football"

# Repertoire de travail pour tous les fichiers de l'app
WORKDIR /app

# Copie des dependances pour optimiser le cache Docker
# Si package.json ne change pas, cette couche est reutilisee
COPY package*.json ./

# Installation des dependances de production uniquement
# npm ci est plus rapide et deterministe que npm install
RUN npm ci --only=production && npm cache clean --force

# Copie du code source de l'application
COPY . .

# Creation d'un utilisateur non-root pour la securite
RUN addgroup -g 1001 -S nodejs && \
    adduser -S nodejs -u 1001 && \
    chown -R nodejs:nodejs /app

# Changement vers l'utilisateur non-root
USER nodejs

# Port sur lequel l'application ecoute
EXPOSE 3000

# Health check pour Docker
HEALTHCHECK --interval=30s --timeout=3s --start-period=5s --retries=3 \
  CMD wget --no-verbose --tries=1 --spider http://localhost:3000/health || exit 1

# Commande de demarrage de l'application
CMD ["node", "app.js"]
```

### 2. README pour Docker

Creez `DOCKER_README.md` :

```markdown
# 🐳 Guide Docker - API REST

## Prérequis
- Docker Desktop ou Docker Engine
- 4 Go de RAM minimum

## Démarrage rapide

### Avec Docker
```bash
# Build
docker build -t api-rest:1.0 .

# Run
docker run -d -p 3000:3000 --name api api-rest:1.0

# Test
curl http://localhost:3000/api/equipes
```

### Avec Docker Compose
```bash
# Démarrer
docker compose up -d

# Voir les logs
docker compose logs -f

# Arrêter
docker compose down
```

## Variables d'environnement

| Variable | Description | Défaut |
|----------|-------------|--------|
| `NODE_ENV` | Environnement | `production` |
| `PORT` | Port d'écoute | `3000` |
| `MONGODB_URI` | URL MongoDB | - |

## Volumes

- `/app/data` : Fichiers de données JSON
- `/app/logs` : Logs de l'application

## Ports

- `3000` : API REST
- `27017` : MongoDB (si utilisé)
- `8081` : Mongo Express (si utilisé)

## Troubleshooting

### Port déjà utilisé
```bash
# Changer le port
docker run -p 3001:3000 api-rest:1.0
```

### Logs
```bash
docker logs api
docker logs -f api  # Suivre en temps réel
```
```

---

## 8. CHECKLIST DE DEPLOIEMENT

### ✅ Avant le déploiement

#### Sécurité
- [ ] Images scannées avec Trivy
- [ ] Aucun secret dans le code ou Dockerfile
- [ ] Utilisateur non-root configuré
- [ ] Ressources limitées (CPU, RAM)
- [ ] Health checks configurés

#### Performance
- [ ] Image optimisée (Alpine, multi-stage)
- [ ] .dockerignore créé
- [ ] Cache Docker optimisé
- [ ] Logs configurés

#### Kubernetes
- [ ] Replicas configurés (min 2)
- [ ] HPA configuré (si nécessaire)
- [ ] Probes (liveness, readiness) configurés
- [ ] Resources (requests, limits) définies
- [ ] PVC pour données persistantes
- [ ] ConfigMap et Secrets créés

#### Monitoring
- [ ] Health endpoint implémenté
- [ ] Logs centralisés
- [ ] Metrics exposées
- [ ] Alertes configurées

---

## 9. COMMANDES PRATIQUES

### Docker

```bash
# Nettoyage
docker system prune -a              # Tout nettoyer
docker container prune              # Conteneurs arrêtés
docker image prune -a               # Images non utilisées
docker volume prune                 # Volumes non utilisés

# Inspection
docker stats                        # Statistiques en temps réel
docker inspect <container>          # Détails d'un conteneur
docker top <container>              # Processus d'un conteneur

# Images
docker images                       # Lister les images
docker history <image>              # Historique d'une image
docker image inspect <image>        # Détails d'une image

# Registry
docker login                        # Se connecter à Docker Hub
docker tag <image> user/image:tag   # Tagger une image
docker push user/image:tag          # Pusher sur Docker Hub
docker pull user/image:tag          # Télécharger une image
```

### Kubernetes

```bash
# Contexte
kubectl config get-contexts         # Lister les contextes
kubectl config use-context minikube # Changer de contexte

# Resources
kubectl get all -A                  # Tout dans tous les namespaces
kubectl get pods -w                 # Watch mode
kubectl get pods -o wide            # Informations étendues
kubectl get pods --show-labels      # Avec labels

# Debugging
kubectl describe pod <pod>          # Détails d'un pod
kubectl logs <pod> --previous       # Logs du conteneur précédent
kubectl exec -it <pod> -- sh        # Shell dans un pod
kubectl port-forward <pod> 8080:3000 # Port forwarding

# Editing
kubectl edit deployment <deploy>    # Éditer en direct
kubectl scale deployment <deploy> --replicas=5  # Scaler
kubectl rollout restart deployment <deploy>     # Redémarrer

# Cleanup
kubectl delete pod <pod>            # Supprimer un pod
kubectl delete all --all            # Supprimer tout (attention!)
```

---

## 10. RESSOURCES UTILES

### Documentation officielle

- **Docker** : https://docs.docker.com
- **Kubernetes** : https://kubernetes.io/docs
- **Docker Hub** : https://hub.docker.com
- **Minikube** : https://minikube.sigs.k8s.io

### Outils

- **Lens** : IDE pour Kubernetes (https://k8slens.dev)
- **K9s** : CLI interactif pour K8s (https://k9scli.io)
- **Portainer** : UI pour Docker (https://www.portainer.io)
- **Dive** : Analyser les images Docker (https://github.com/wagoodman/dive)

### Tutoriels

- Play with Docker : https://labs.play-with-docker.com
- Katacoda Kubernetes : https://www.katacoda.com/courses/kubernetes
- Docker Curriculum : https://docker-curriculum.com

---

# CONCLUSION

## 🎉 FELICITATIONS !

Vous avez maintenant une connaissance complete de :

### ✅ Docker
- Concepts fondamentaux (images, conteneurs, volumes, networks)
- Création de Dockerfile optimisés
- Docker Compose pour multi-conteneurs
- Bonnes pratiques de sécurité et performance

### ✅ Kubernetes
- Architecture et concepts (Pods, Services, Deployments)
- Déploiement d'applications
- Scaling automatique (HPA)
- Configuration (ConfigMap, Secrets)
- Volumes persistants
- Ingress et routage

### ✅ DevOps
- CI/CD avec Docker
- Monitoring et logging
- Backup et restauration
- Optimisation et performance

---

## 🚀 PROCHAINES ETAPES

### Niveau Intermédiaire

1. **Helm** : Package manager pour Kubernetes
2. **Istio** : Service mesh pour Kubernetes
3. **ArgoCD** : GitOps pour Kubernetes
4. **Rancher** : Gestion multi-clusters

### Niveau Avancé

1. **Amazon EKS** : Kubernetes managé sur AWS
2. **Google GKE** : Kubernetes sur Google Cloud
3. **Azure AKS** : Kubernetes sur Azure
4. **Service Mesh** : Istio, Linkerd
5. **Observability** : OpenTelemetry, Jaeger

### Projets pratiques

1. Déployer l'API REST sur un cloud provider
2. Mettre en place un pipeline CI/CD complet
3. Implémenter un service mesh avec Istio
4. Créer un cluster Kubernetes multi-nodes
5. Configurer une stack de monitoring complète

---

## 📚 RECAPITULATIF DU PARCOURS

### Vous avez appris à :

1. **Installer et configurer** Docker et Kubernetes
2. **Conteneuriser** une application Node.js
3. **Orchestrer** plusieurs conteneurs avec Docker Compose
4. **Déployer** sur Kubernetes avec Minikube
5. **Scaler** automatiquement avec HPA
6. **Sécuriser** vos conteneurs et clusters
7. **Monitorer** et logger vos applications
8. **Optimiser** les images et les performances

---

## 💪 COMPETENCES ACQUISES

✅ Maîtrise de Docker (images, conteneurs, volumes, networks)
✅ Maîtrise de Docker Compose (orchestration multi-conteneurs)
✅ Maîtrise de Kubernetes (Pods, Services, Deployments, ConfigMaps, Secrets)
✅ Compréhension du DevOps (CI/CD, monitoring, logging)
✅ Bonnes pratiques de sécurité et performance
✅ Capacité à déployer en production

---

## 📞 BESOIN D'AIDE ?

- **Docker Community** : https://forums.docker.com
- **Kubernetes Slack** : https://slack.k8s.io
- **Stack Overflow** : Tags `docker`, `kubernetes`
- **Reddit** : r/docker, r/kubernetes

---

## 🎯 CERTIFICATS RECOMMANDES

- **Docker Certified Associate (DCA)**
- **Certified Kubernetes Administrator (CKA)**
- **Certified Kubernetes Application Developer (CKAD)**

---

**Bon courage dans votre voyage DevOps ! 🚀**

---

## ANNEXES

### Glossaire

- **Container** : Unité logicielle standardisée
- **Image** : Template pour créer des conteneurs
- **Pod** : Plus petite unité déployable dans K8s
- **Service** : Abstraction pour exposer des Pods
- **Deployment** : Gestion déclarative des Pods
- **HPA** : Horizontal Pod Autoscaler
- **Ingress** : Routage HTTP/HTTPS
- **ConfigMap** : Configuration non-sensible
- **Secret** : Configuration sensible (encodée)
- **PV/PVC** : Persistent Volume / Claim

### Fichiers du projet

```
node-js/
├── app.js
├── package.json
├── Dockerfile
├── .dockerignore
├── docker-compose.yml
├── .env
├── k8s/
│   ├── deployment.yaml
│   ├── service.yaml
│   ├── configmap.yaml
│   ├── secret.yaml
│   ├── hpa.yaml
│   ├── ingress.yaml
│   └── all-in-one.yaml
├── data/
│   ├── equipes.json
│   └── marketData.json
├── routes/
│   ├── equipesRoutes.js
│   └── marketRoutes.js
└── public/
    ├── index.html
    └── script.js
```

---

**FIN DU GUIDE**

Ce guide a été créé pour accompagner votre apprentissage de la conteneurisation et de l'orchestration. N'hésitez pas à expérimenter et à adapter les exemples à vos besoins spécifiques !

**Bonne chance ! 🐳☸️**
