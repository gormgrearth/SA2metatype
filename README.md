# Métatypes — Shadowrun Anarchy (non officiel)

Base de données communautaire de métatypes, en un seul fichier HTML autonome
(`index.html`), prête à héberger sur **GitHub Pages** et branchée sur
**Firebase** (Firestore pour les données, Authentication pour l'accès admin).

Sans configuration, le fichier s'ouvre quand même : il tourne alors en
**mode démo** (quelques métatypes d'exemple, lecture seule) pour que vous
puissiez voir le rendu immédiatement.

## 1. Structure du dépôt conseillée

```
/
├── index.html          ← le fichier fourni
├── images/              ← vos illustrations de métatypes
│   ├── elfe-dalakitnon.png
│   ├── humain-classique.png
│   └── ...
├── firestore.rules      ← règles de sécurité Firestore (fournies)
└── README.md
```

### Nommage des illustrations
Pour rester lisible dans le dépôt, nommez vos fichiers :
`images/<categorie>-<metatype>.png` (minuscules, tirets, sans accents),
par exemple `images/elfe-dalakitnon.png` ou `images/troll-ogre.png`.
Le formulaire d'ajout du site vous suggère automatiquement ce nom au fur et
à mesure que vous saisissez le métatype et la catégorie — il vous suffit de
déposer le fichier au même endroit dans `/images` puis de coller le même
chemin dans le champ « Illustration ».

Une URL complète (`https://...`) fonctionne aussi si vous préférez héberger
les images ailleurs.

## 2. Créer le projet Firebase

1. Allez sur <https://console.firebase.google.com> et créez un projet
   (gratuit, offre Spark suffisante pour ce cas d'usage).
2. Dans **Compilation > Firestore Database**, créez une base de données
   (mode production).
3. Dans **Compilation > Authentication**, activez le fournisseur
   **E-mail/mot de passe**, puis dans l'onglet **Users**, créez manuellement
   **un seul compte** avec l'e-mail et le mot de passe que vous utiliserez
   pour administrer le site (c'est ce compte qui fera office de compte
   admin — il n'y a pas de rôle séparé à configurer).
4. Dans **Paramètres du projet > Vos applications**, ajoutez une application
   **Web** (icône `</>`). Copiez l'objet de configuration fourni.

## 3. Configurer `index.html`

Ouvrez `index.html` et repérez, en haut du `<script type="module">`, le bloc :

```js
const firebaseConfig = {
  apiKey: "YOUR_API_KEY",
  authDomain: "YOUR_PROJECT.firebaseapp.com",
  projectId: "YOUR_PROJECT",
  storageBucket: "YOUR_PROJECT.appspot.com",
  messagingSenderId: "YOUR_SENDER_ID",
  appId: "YOUR_APP_ID"
};
```

Remplacez ces valeurs par celles de votre application Web Firebase. Dès que
`apiKey` n'est plus `"YOUR_API_KEY"`, le site quitte automatiquement le mode
démo et se connecte à votre Firestore.

## 4. Appliquer les règles de sécurité Firestore

Dans la Console Firebase, **Firestore Database > Règles**, collez le
contenu du fichier `firestore.rules` fourni :

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /metatypes/{docId} {
      allow read: if true;
      allow write: if request.auth != null;
    }
  }
}
```

Cela veut dire : **tout le monde peut consulter** les métatypes, mais
**seule une personne connectée** (donc, dans ce projet, votre compte admin)
peut ajouter, modifier ou supprimer.

## 5. Déployer sur GitHub Pages

1. Poussez `index.html`, `images/` et ce `README.md` dans un dépôt GitHub.
2. Dans **Settings > Pages** du dépôt, choisissez la branche (`main`) et le
   dossier racine `/`.
3. Votre site est en ligne à l'adresse fournie par GitHub
   (`https://<utilisateur>.github.io/<depot>/`).

## 6. Utilisation

- **Visiteurs** : parcourent, recherchent et filtrent les métatypes par
  catégorie ; cliquent sur une fiche pour voir le détail (description,
  caractéristiques FOR/AGI/VOL/LOG/CHA/ANA, traits).
- **Admin** : cliquez sur « Connexion admin » en haut à droite, connectez-vous
  avec le compte créé à l'étape 2. Les boutons « + Ajouter un métatype »,
  « Modifier » et « Supprimer » apparaissent alors sur les fiches.

## Notes

- Les champs caractéristiques (FOR, AGI, VOL, LOG, CHA, ANA) sont stockés en
  tant que nombres ; ajustez-les librement selon vos propres règles maison.
- Les traits sont une liste libre de lignes de texte (vous pouvez y indiquer
  un coût entre crochets, comme dans le classeur d'origine, ex.
  `Vision nocturne [0]`).
- Ce projet est un outil de fan, sans lien avec Topps/FASA, l'éditeur de
  Shadowrun.
