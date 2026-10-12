# Journal

Suite du journal de `js-exercices` (Jours 1 à 14).

## Jour 15 :

- 09/10/2026
  React, partie 1

### Mise en place du projet du cours

- Projet du formateur récupéré dans `cours-react-1/` (create-react-app, React 17, react-router-dom 6, axios, Sass)
- Installation avec `npm install`, lancement avec `npm start`
- Cours suivi en vidéo, sans refaire le TP

#### Points d'attention

- **index.js** : la syntaxe `react-dom/client` / `createRoot` est celle de React 18, alors que le projet est en React 17 (`ReactDOM.render`). Le code doit correspondre à la version installée.
- **API REST Countries non valide** : le TP du formateur appelle `https://restcountries.com/v3.1/all`. Cette version de l'API est aujourd'hui dépréciée : elle renvoie une erreur, que le navigateur affiche comme une erreur CORS. Le site n'affiche donc plus de pays.
- **Choix** : je n'ai pas modifié le code. J'ai suivi le cours tel que le formateur l'a codé, en conservant son lien d'origine, afin de suivre la même logique.
- Leçon retenue : une API publique peut être dépréciée, et une erreur CORS peut masquer une vraie erreur serveur. Il faut vérifier la réponse dans l'onglet Network.

## Notions React vues (cours 1)

### Présentation & installation (0:00)

- Projet créé avec create-react-app, React 17.
- `npm install` puis `npm start` : serveur de dev sur localhost:3000.
- Structure : `public/` (index.html, fichiers statiques), `src/` (index.js = point d'entrée, composants, styles Sass).
- Point d'entrée en React 17 : `ReactDOM.render(<App />, document.getElementById("root"))`.
- [Notion de composant : fonction qui retourne du JSX. À compléter avec l'exemple du formateur.]

### Router & navigation (32:14)

- Paquet : `react-router-dom` v6.
- `BrowserRouter` autour de l'app, `Routes` + `Route path="..." element={<Page />}`.
- Navigation sans rechargement avec `Link` / `NavLink` (NavLink pour l'état « actif »).
- v6 : `element={<Composant />}` remplace `component=` de la v5, et `Routes` remplace `Switch`.
- [Route 404 / `path="*"` : à noter si le formateur la montre.]

### Les images (52:59)

- Image importée depuis `src/` : `import logo from "./logo.png"` puis `<img src={logo} />`.
- Image dans `public/` : chemin direct depuis la racine.
- [Préciser laquelle des deux méthodes est utilisée dans le projet.]

### Axios : useEffect & useState (59:53)

- `useState` : stocker les données reçues (`const [data, setData] = useState([])`).
- `useEffect` avec tableau de dépendances `[]` : l'appel API ne se fait qu'au montage.
- `axios.get(url).then(res => setData(res.data))`.
- Piège : sans le `[]`, le composant se re-rend, relance l'appel, boucle infinie.
- Piège : l'API REST Countries utilisée par le cours est dépréciée, voir la note plus haut.

### Map & props (1:13:13)

- `data.map(item => <Card key={...} country={item} />)` pour afficher une liste.
- `key` obligatoire et unique sur chaque élément d'une liste.
- Props : données passées du parent à l'enfant (`<Card country={item} />`), lues via `props.country` ou par déstructuration.
- Les props sont en lecture seule.

### Les filtres : slice, filter, sort (1:31:12)

- `slice(0, n)` : limiter le nombre d'éléments affichés.
- `filter(...)` : garder les éléments qui répondent à une condition (recherche par nom, par continent...).
- `sort((a, b) => ...)` : trier. Attention : `sort` modifie le tableau d'origine, faire une copie avant (`[...data].sort(...)`).
- Enchaînement typique : `data.filter(...).sort(...).slice(...).map(...)`.
- [État du formulaire (input contrôlé, range, radio) : à noter si le formateur le montre.]

## Jour 16

– 12/10/2026
Cours React 2 : page blog avec faux back

### Ce que j'ai fait

- Cours terminé, projet dans `cours-react-2/` : suite du site du cours 1, avec une page blog opérationnelle.
- Même stack que le cours 1 (React 17.0.2, react-router-dom 6.2.2, axios 0.26, Sass) + json-server 0.17.

### Lancer le projet

- `npm install`
- Terminal 1 : `npm start` (React, localhost:3000)
- Terminal 2 : `npm run server` (json-server, localhost:3003, données dans `src/assets/db.json`)

### Architecture : deux composants

- `pages/Blog.js` : la page. Elle charge la liste des articles, contient le formulaire de création et affiche les articles.
- `components/Article.js` : un article. Il gère son propre affichage, son mode édition et sa suppression.
- Le parent `Blog` passe chaque article à `Article` en props (`<Article key={article.id} article={article} />`).

### Blog.js : charger, créer, afficher

**1. Les états (`useState`)**

- `blogData` : la liste des articles reçus de l'API.
- `author` et `content` : les champs du formulaire (inputs contrôlés : `value` + `onChange`).
- `error` : `true` si le message fait moins de 140 caractères.

**2. Charger les articles (GET)**

```js
const getData = () => {
  axios
    .get("http://localhost:3003/articles")
    .then((res) => setBlogData(res.data));
};
useEffect(() => getData(), []);
```

`getData` est une fonction à part, car on la rappelle après chaque création. Le `[]` lance le premier chargement une seule fois, au montage.

**3. Créer un article (POST) avec validation**

- `e.preventDefault()` empêche le rechargement de la page à l'envoi du formulaire.
- Si `content.length < 140` : `setError(true)`, rien n'est envoyé.
- Sinon : `axios.post(...)` avec `author`, `content` et `date: Date.now()` (un timestamp en millisecondes). Puis on vide les champs et on recharge la liste.
- Retour visuel de l'erreur : bordure rouge sur le `textarea` (`style` conditionnel) et message affiché avec `{error && <p>...</p>}`.

**4. Afficher la liste**

```js
blogData
  .sort((a, b) => b.date - a.date)
  .map((article) => <Article key={article.id} article={article} />);
```

`sort` classe du plus récent au plus ancien grâce au timestamp. `key={article.id}` : l'id est fourni par json-server.

### Article.js : afficher, éditer, supprimer

- **Format de la date** : `dateFormater` transforme le timestamp en date lisible avec `toLocaleDateString("fr-FR", {...})`.
- **Mode édition** : l'état `isEditing` bascule entre `<p>` (lecture) et `<textarea>` (édition), avec des ternaires. Le fond de la carte change aussi (`#f3feff` en édition).
- **Modifier (PUT)** : `handleEdit` envoie l'article complet avec le nouveau contenu (ou l'ancien si rien n'a changé) et un champ `updatedDate`, puis repasse `isEditing` à `false`.
- **Supprimer (DELETE)** : `window.confirm` demande confirmation, puis `axios.delete` sur `/articles/<id>`, et `window.location.reload()` recharge la page pour mettre la liste à jour.

### Routes json-server utilisées

| Action        | Méthode | URL             |
| ------------- | ------- | --------------- |
| Lire la liste | GET     | `/articles`     |
| Créer         | POST    | `/articles`     |
| Modifier      | PUT     | `/articles/:id` |
| Supprimer     | DELETE  | `/articles/:id` |

PUT remplace tout l'objet : c'est pourquoi on renvoie `author` et `date` en plus du nouveau contenu.

### Points d'amélioration repérés dans le code

- **POST puis GET sans attendre** : `getData()` est appelé juste après `axios.post(...)`, sans attendre la réponse. La liste peut se recharger avant que l'article soit enregistré. Plus sûr : `axios.post(...).then(() => getData())`.
- **`window.location.reload()` après suppression** : ça marche, mais recharge toute la page. Alternative : rappeler la fonction de chargement, ou retirer l'article de l'état.
- **`sort` modifie le tableau d'état** : à faire sur une copie (`[...blogData].sort(...)`).
- **`updatedDate` est enregistré mais jamais affiché.**
- **Faute dans le bouton** : « Suprpimer » à corriger en « Supprimer ».
