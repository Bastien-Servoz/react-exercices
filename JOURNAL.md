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
