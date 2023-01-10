```$
npm init react-app first-app
npx create-react-app my-app --template typescript
npm run start 
```

En react.js, tous les éléments sont des fonctions JS qui retourne du JSx (Version étandu de HTML)

# Logique d'un Framework JS

Utiliser un composant parent qui gère la logique et des enfants qui gèrent l'affichage.

## Les Hooks

On appel hooks, une fonction qui suit l'état local d'une virable, notement mettre a jour des variables du DOM.

- ### useState
useState permet de mettre à jour une variable. Elle prend en parametre une valeur initial pour la variable, elle a en suite une fontion pour modifier cette valeur. 
```js
import { useState } from 'react';

const [maVar, setMaVar] = useState(0)

const onClickHandler = () => {
  setMaVar((oldVar) => {
    return oldVar + 1
  })
}
```

## Affichage conditionelle

Faire de l'affichage en fonction d'une condition booleen :
```js
{ Expression_booleen && JSX }
```
Affiche le contenu de JSX seulement si Expression_booleen est true 