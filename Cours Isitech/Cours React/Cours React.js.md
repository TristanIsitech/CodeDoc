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
```jsx
import { useState } from 'react';

const [maVar, setMaVar] = useState(0)

const onClickHandler = () => {
  setMaVar((oldVar) => {
    return oldVar + 1
  })
}
```

- ### useReducer
useReducer est similaire a useState, il sert pour les valeurs plus grandes, tableau et object complexes ... 
```jsx
const myReducer = (state, action) => {
        switch (action.type) {
            case 'MODFIY_TEST_STRING':
                console.log('je suis entré dans MODFIY_TEST_STRING')
                return {...state, test: action.payload}
            default:
                console.log('je suis pas entré dans un case')
                return state
        }
    }

    const [state, dispatch] = useReducer(myReducer, {test: 'myString'})

    const myHandler = () => {
        dispatch({type: 'MODFIY_TEST_STRING', payload: 'New Value'})
    }
```

- ### useEffect
useEffect est un hooks qui vas s'appliquer a chaque mise a jour du DOM. Attention a ne pas faire de boucle infinit !! Il nécéssite parfois de passer un tableau en dépendence (valeur mise a jour dans la fonction) pour ne pas créer de boucle infinit. 
```jsx
useEffect(() => {
	        axios.get("https://path.com)
	            .then((res) => {
	                     navigate('/game')
	                     })
	        }
	    }, [navigate])
```

Dans cette exemple, on utilise useEffect pour faire une requete axios a chaque mise a jour du dom, puis on lui passe navigate comme dépendence. 

## Affichage conditionelle

Faire de l'affichage en fonction d'une condition booleen :
```jsx
{ Expression_booleen && JSX }
{ Expression_booleen ? JSX_si_true : JSX_sinon }
```

# Store

Le store est un fichier js qui stoque les variables nécéssaire au fonction de l'application qui ont besoin d'être partagées entre plusieurs composant.
Le store permet, dans une architechture React complexe de ne pas passer plein de props d'enfant vers parents, et inversement. 
Le store peut être appeler de n'importe qu'elle composant et mis a jour depuis n'importe lequel.

## appContext.js
appContext.js est le fichier du store, il se découpe en 2 parties :

- #### myAppConext
Une fonction dans laquelle sont définit toutes les variables dont l'application a besoin, ainsi que leurs fonction de dispatch.
```jsx
export const myAppContext = createContext({
	var1: null,
	dispatchVar1: (action) => { },
	var2: {},
	dispatchVar2: (action) => { }
})
```

- #### MyAppContextProvider
Une fonction qui contient les fonctions Reducer de chaque variable, ainsi que leurs return dans un composant parent. Les balise de ce composant parent permette a tout le enfant d'hériter de ces paramètre.
```jsx
export const MyAppContextProvider = ({ children }) => {
	const var1Reducer = (state, action) => {
		// update de var1
	}
	
	const var2Reducer = (state, action) => {
		// update de var2
	}
	
	const [var1, dispatchVar1] = useReducer(var1Reducer, null)
	const [var2, dispatchVar2] = useReducer(var2Reducer, {})
	
	return (
		<myAppContext.Provider value={{
			var1: var1,
			dispatchVar1: dispatchVar1,
			var2: var2,
			dispatchVar2: dispatchVar2
		}}>
			{children}
		</myAppContext.Provider>
	)
}
```

## Import du store

#### Placement du composant parent

On le place généralement a la racine des composants pour permettre a chacun des enfants de bénéficer de toutes les variables du store, néanmoins on peut les mettres autres de part de façon a ne faire hériter des variables qu'une partie de l'application.

Dans le index.js :
```jsx
import { MyAppContextProvider } from './Store/appContext.js'

const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(
	<MyAppContextProvider>
		<App />
	</MyAppContextProvider>
);
```

#### Import dans l'application

On import myAppcontext :
```jsx
import { myAppContext } from './Store/appContext.js';
```

Puis on définit context :
```jsx
	const context = useContext(myAppContext)
```

Enfin on peut utiliser les varibles du store :
```jsx
	context.var1
	context.dispatchVar1({tpye: "", payload: {}})
```
# Router

Le router est un composant qui permet de définir des routes vers d'autres composants, cela permet d'utiliser l'url pour modifier la vue, soit la totalité, soit une partie.

## NPM

```cmd
$ npm install react-router-dom@6
```

## Fonctionnement des routes

### Import

Dans le fichier index.js :
```jsx
import { BrowserRouter } from 'react-router-dom';

const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(
	<BrowserRouter>
		<App />
	</BrowserRouter>
);
```

Evidement, on peut combiné le store et le router, ainsi même que d'autre composant parent qui permettent a leurs enfant d'hériter de certaine fonction.
Ainsi, de même que le store, le router ne peut être importé dans n'importe quel composant, pour ne servire qu'a un endroit particulier. 

### Utilisation 

Dans le fichier nécessitant une route :
- Il faut d'abord importer les composants nécéssaires pour chaque route.
- Il faut importer les composants de Router.
- Placer un composant ```<Routes> </Routes>``` qui vas permettre de changer les routes a l'interrieur.
- Placer a l'interrieur des composants ```<Route />```  qui prennent deux paramètres :
	- `path="/"` qui est la route url qui vas servire a pointer vers ce composant. La route `"/"` définit la route par défault, la route ``"*"``  définit la route vide, ou n'importe qu'elle route. Les routes sont prisent dans l'ordre, on peut donc gérer les routes inexistantes avec `"*"` en plassant cette route en dernière, si aucune des routes précedantes n'est la bonnes, c'est la dernière qui est lu. Le route `"/*"` permet de définir la route par défault et toutes les routes a l'interrieur, en cas de route combinée par exemple.
	- `element={<Composant />}` qui est la paramètre qui vas permettre a la route de pointer vers le composant en question. 

### Exemple de route

#### Import
```jsx
import { Routes, Route } from 'react-router-dom';
```

#### Route simple
```jsx
<Routes>
	<Route path="/" element={<Accueil />} />
	<Route path="/composant1" element={<Composant1 />} />
	<Route path="*" element={<Error />} />
</Routes>
```

#### Route combiné
- App.js
```jsx
<Routes>
	<Route path="/*" element={<Accueil />} />
	<Route path="/composant1/*" element={<Composant1 />} />
	<Route path="*" element={<Error />} />
</Routes>
```

- Acceuil.js
```jsx
<Routes>
	<Route path="*" element={<Accueil />} />
	<Route path="/composant2" element={<Composant2 />} />
</Routes>
```

- Composant1.js
```jsx
<Routes>
	<Route path="/page1" element={<Page1 />} />
	<Route path="/page2" element={<Page2 />} />
	<Route path="/page3" element={<Page3 />} />
</Routes>
```

- ...

## Link

L'outil Link permet de créer des mouvements entre les pages, il est l'équivalent des balises `<a />` en HTML.

Pour l'utiliser, il suffit d'utiliser le composant `<Link> </Link>` autour d'un autre composant. Link prend un paramètre : `to="/"` dans lequel on difinit la route vers laquelle il vas rediriger.

#### Exemple 
```jsx
import { Link } from 'react-router-dom';
```

```jsx
<Link to="/">
	<button> Accueil </button>
</Link>
```

## useNavigate

useNavigate est un Hooks, de Router qui permet de faire la redirection de route.

Il suiffit de l'importer et de le définir, puis on appel la fonction, qui vas prendre en paramètre la route.

#### Exemple
```jsx
import { useNavigate } from 'react-router-dom';
```

```jsx
const navigate = useNavigate()

const aFuntion = () => {
	navigate('/Accueil')
}
```