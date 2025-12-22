# TD : Découverte des API REST avec Node.js et Express
**Niveau :** Débutant  
**Durée estimée :** 30 - 45 minutes

---

## Objectif
Comprendre comment créer une petite application où :
1. **Node.js (Serveur)** va chercher des données sur une API distante.
2. **HTML/JS (Client)** affiche ces données sans recharger la page.

Nous n'utiliserons pas de moteur de template complexe, juste du HTML et du JavaScript standard.

---

## 1. Architecture de l'application

Voici comment cela va fonctionner :
1. Le **Navigateur** demande la page d'accueil (`index.html`).
2. La page **HTML** contient un script **JS** qui demande les données à notre serveur Node.js.
3. Le serveur **Node.js** demande les données à l'API externe (JSONPlaceholder).
4. Le serveur **Node.js** renvoie les données au navigateur.
5. Le **JavaScript** du navigateur crée les cartes et les affiche.

---

## 2. Préparation du projet

### Étape 1 : Créer le dossier
Créez un nouveau dossier nommé `mon-td-api`.
Ouvrez ce dossier avec VS Code.

### Étape 2 : Initialiser le projet
Ouvrez le terminal dans VS Code et tapez :

```bash
npm init -y
```

### Étape 3 : Installer les outils
Nous avons besoin de 1 seul outil :
1. **express** : Pour créer notre serveur web.

*(Note : Nous utiliserons `fetch` qui est inclus dans Node.js pour récupérer les données, donc pas besoin d'installer autre chose !)*

Tapez dans le terminal :
```bash
npm install express
```

---

## 3. Le Serveur (Node.js)

Créez un fichier nommé `app.js` à la racine.
Ce serveur va servir les fichiers statiques (HTML) et agir comme une passerelle pour les données.

```javascript
const express = require('express');
const app = express();

// 1. On indique à Express de servir les fichiers du dossier "public" (notre HTML/CSS/JS)
app.use(express.static('public'));

// 2. On crée une route API pour notre site
// Notre site appellera cette route pour avoir les données
app.get('/api/utilisateurs', async (req, res) => {
    try {
        console.log('Le serveur récupère les données...');
        
        // Le serveur va chercher les infos chez JSONPlaceholder avec fetch (natif)
        const reponse = await fetch('https://jsonplaceholder.typicode.com/users');
        
        // On transforme la réponse en JSON
        const utilisateurs = await reponse.json();
        
        // Le serveur renvoie les données au navigateur
        res.json(utilisateurs);
        
    } catch (error) {
        console.error(error);
        res.status(500).send("Erreur serveur");
    }
});

// 3. Démarrage du serveur
app.listen(3000, () => {
    console.log('Serveur lancé sur http://localhost:3000');
});
```

---

## 4. L'Interface (HTML + JavaScript)

Nous allons séparer le code. Créez un dossier nommé `public` à la racine du projet.
Dans ce dossier `public`, créez un fichier `index.html`.

### Le fichier `public/index.html`

```html
<!DOCTYPE html>
<html lang="fr">
<head>
    <meta charset="UTF-8">
    <title>Mes Utilisateurs API</title>
    <style>
        body { font-family: sans-serif; background-color: #f4f4f9; padding: 20px; }
        h1 { text-align: center; color: #333; }
        button { display: block; margin: 0 auto 20px; padding: 10px 20px; cursor: pointer; }
        
        .container { 
            display: flex; 
            flex-wrap: wrap; 
            justify-content: center; 
            gap: 20px; 
        }
        
        .carte {
            background: white;
            border-radius: 8px;
            padding: 20px;
            width: 250px;
            box-shadow: 0 2px 4px rgba(0,0,0,0.1);
            transition: transform 0.2s;
        }
        .carte:hover { transform: translateY(-5px); }
        .nom { color: #2c3e50; font-weight: bold; font-size: 1.1em; margin-bottom: 5px; }
        .email { color: #7f8c8d; font-size: 0.9em; margin-bottom: 10px; }
    </style>
</head>
<body>

    <h1>Annuaire des Utilisateurs</h1>
    
    <!-- Un bouton pour charger les données -->
    <button onclick="chargerUtilisateurs()">Charger la liste</button>

    <!-- L'endroit où les cartes vont s'afficher -->
    <div class="container" id="liste-utilisateurs">
        <!-- Les cartes seront ajoutées ici par le JavaScript -->
    </div>

    <script>
        // Fonction appelée quand on clique sur le bouton
        async function chargerUtilisateurs() {
            const conteneur = document.getElementById('liste-utilisateurs');
            conteneur.innerHTML = '<p>Chargement en cours...</p>';

            try {
                // 1. On demande les données à NOTRE serveur Node.js
                const reponse = await fetch('/api/utilisateurs');
                const utilisateurs = await reponse.json();

                // 2. On vide le message de chargement
                conteneur.innerHTML = '';

                // 3. Pour chaque utilisateur, on crée le HTML
                utilisateurs.forEach(user => {
                    // Création de la div carte
                    const carte = document.createElement('div');
                    carte.className = 'carte';

                    // Remplissage du contenu
                    carte.innerHTML = `
                        <div class="nom">${user.name}</div>
                        <div class="email">${user.email}</div>
                        <div> ${user.company.name}</div>
                        <div> ${user.phone}</div>
                    `;

                    // Ajout à la page
                    conteneur.appendChild(carte);
                });

            } catch (error) {
                console.error('Erreur:', error);
                conteneur.innerHTML = '<p style="color:red">Erreur lors du chargement.</p>';
            }
        }
    </script>
</body>
</html>
```

---

## 5. Lancer et Tester

1. Dans le terminal :
```bash
node app.js
```
2. Ouvrez votre navigateur sur **http://localhost:3000**
3. Cliquez sur le bouton "Charger la liste".
4. Observez : La page ne se recharge pas, mais les données apparaissent !

---

## 6. Exercices pour aller plus loin

1. **Ajouter du style** : Modifiez le CSS pour changer la couleur des cartes selon si l'utilisateur a un site web (`.com`, `.org`...).
2. **Nouvelle route** : Ajoutez une route `/api/posts` dans `app.js` pour récupérer les articles (`https://jsonplaceholder.typicode.com/posts`) et créez un deuxième bouton dans le HTML pour les afficher.
