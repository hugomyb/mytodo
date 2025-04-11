# Déploiement d'applications avec Scalingo

## Etapes

### 1. Création de l'application

- Se connecter à https://dashboard.scalingo.com/
- Cliquer sur **"Create an application"**
- Choisir une région, donner un nom à l'application
- Lier le repo GitHub
- Lancer le déploiement initial via l'interface Scalingo

---

### 2. Ajout de l'addon MongoDB

- Aller dans l'application Scalingo
- Onglet **"Addons"**
- Ajouter **MongoDB**
- Une fois ajouté, une variable d’environnement `SCALINGO_MONGO_URL` est automatiquement créée

---

### 3. Modification du code pour utiliser SCALINGO_MONGO_URL

#### `lib/db-mongo.js` :

- Remplacer la logique de connexion manuelle (host, user, password, etc.) par :

```js
MongoClient.connect(credentials.SCALINGO_MONGO_URL, {
  useNewUrlParser: true,
  useUnifiedTopology: true
}, (err, client) => {
  if (err) return reject(err);
  db = client.db().collection('todos');
  resolve();
});
```

#### `server.js` :

Ajouter cette condition pour gérer la connexion via Scalingo :

```js
else if (process.env.SCALINGO_MONGO_URL !== undefined) {
  db = require('./lib/db-mongo')({ SCALINGO_MONGO_URL: process.env.SCALINGO_MONGO_URL });
}
```

### 4. Déploiement

Une fois les modifications push sur GitHub, Scalingo redéploie automatiquement.

### 5. Vérification

Accéder à l’URL de l’app : https://mytodo-hugom.osc-fr1.scalingo.io/ et tester la persistance des données.

Ou alors accéder à l'URL https://mytodo-hugom.osc-fr1.scalingo.io/api/todos et regarder si les todos créés sont bien renvoyés.

