# Définition

Collection : un groupe de document MongoDB. Les documents présents au sein d'une même collection peuvent avoir des champs différents.
Document : ensemble de données clé-valeur (comme un object js)
Le shéma de document est dit dynamique.

# La notation JSON

### Mongo DB utilise la notation JSON classique :

- booleen
- numerique
- chaine de caractères
- tableau
- objet
- null

### Mongo DB ajoute des types :

- le type Date : stocké sous la forme d'un entier signé de 8 octets (correspond au nombre de secondes écoulées depuis l'époque Unix : 01/01/1970)
- le type ObjectId : stocké 12 octets, utilise en interne par MongoDB afin de generer des identifiants
- les types NumberLong et NumberInt : par defaut, MongoDB considère toute valeur numerique comme un nombre à virgule codé sur 8 octets. Representent des entiers signés sur 8 et 4 octets.
- Le type BinData : pour stocker des chaines de caractères ne posssedant pas de representation deasn l'encodage UTF-8, ou n'importe quel contenu binaire. 

# MongoDB en ligne de commande

```bash
mongod

mongod --port 27017

mongod --port 27017 --dbpath /data/database --logpath /tmp/mongodb.log --logappend

sudo service mongodb stop  <-- Sur linux -->
```


# Création d'une base de donnée

```bash
use tmp

db.maCollection.insertOne({
 "une_cle":"une_valeur"
})
```

# Supprimer une base de donnée

```shell
use laDBASupprimer;

db.dropDatabase()
```


# Les helpers

```shell
db.runCommand({"collstats": "uneCollection"})

db.adminCommand("currentOp")
```

# Gestion des collections

## Les collations

```json
{
	locale*: <string>,
	caseLevel: <booleen>,
	casefirst: <string>,
	strength: <entier>,
	numericOrdering: <boolean>,
}
```

Au sein de ce type de document le champ locale est obligatoire.

# Creer une collection 

```shell
db.cereateCollection("maCollection", { "collation": {"local": "fr"}})
```

# Les documents

## Insertion d'un document

```js
db.maCollection.insert(<documents> /*ou tableau de document*/)

db.personnes.insert([
	{
		"nom": "THOMAS",
		"prenom": "Jean"
	},
	{
		"nom": "KENJI",
		"prenom": "Lao",
		"age": 20
	}
])
```

## Modification d'un document

```js
db.collection.update(<filtre>, <modification>)

db.personne.update(
	{"nom": "dupont"},
	{
		$set :  { "nom": "Dupont" }
	},
	// L'option "multi" permet d'en update plusieur
	{
		"multi": true
	}
) //  permet de mettre a jour les personnes qui on pour nom "dupont" en "Dupont"
```

### Update avec l'id :
```js
db.personne.update(
	{"_id_": ObjectId("dupont")},
	{
		$set :  { "nom": "Dupont" }
	}
)
```

### Cas de l'upset : la combinaison d'update et d'insert
```js
db.personnes.updateOne{
	{"prenom": "Remi"},
	{
		$set : {"prenom": "remi"}
	},
	{ "upset": true} // Si inexistant, il créer le champ
}
```

### Mettre a jour tout les doc
```js
db.personnes.updateOne{
	{}, // On lui passe un objet vide
	{
		$set : {"prenom": "remi"}
	}
}
```

## Effectuer des recherches sur la méthode 'findAndModify'

***A Faire***

## Validation des documents

```js
var athletes = [{
 ...
}]

db.athletes.insertMany(athletes)
```


```js
var proprietes = {
	"nom": {
		"bsonType": "string", // Type de la variable
		"pattern": "^[A-Z].*", // Majuscule + chaine de caractère
		"description": "Chaine de caractère+ expr régulière - obligatoire"
	},
	"prenom": {
		...
	},
	"discipline":{
		"enum": ["Course", ...] // Liste des dicipline autorisée
	}
}
```

```js
db.runCommand({
	"collMod": "athletes",
	"validator": {
		$jsonSchema: {
			"bsonType": "object",
			"required": ["nom", "prenom", "discipline"],
			"properties": proprieties
		}
	}
})
```

## L'operateur $expr

Il permet d'utiliser des expressions dans nos requêtes. Ces expressions pourront contenir des operateurs, des objects ou encore des chemins d'objet pointant vers des champs.

```js
db.personnes.find({
	"nom" : { $exists: 1 },
	"age" : { $exists: 1 },
	$expr: { $gt: [ { $multiply: [ { $strLenCp: "$nom" }, 12 ]}, "$age" ] }
},
 {
	 "nom": 1,
	 "_id": 0
 })
```

Afficher les comptes dont la sommes des operations de debit est superieure au montant du credit.

```js
db.banque.find({
	"credit" : { $exists: 1 },
	"debit" : { $exists: 1 },
	$expr: { $gt: [ { $sum: "$debit" }, "$credit" ] }
})
```

## L'operateur $type

```js
{ champ: { $type: <type BSON> } }
{ champ: { $type: [<type BSON>, <type BSON>] } }

db.personnes.inseretOne({
	"nom": "Zidane", "prenom": "Z", "age": numerInt(50)
})
```

## L'operateur $where

Il permet d'executer directement du code js dans un fonction

```js
db.personnes.find({ $where: "tihs.nom.length > 6" })

db.personnes.find({ $where: function() {
	return obj.nom.length > 6
}})
```

## Les operateurs de tableau 

```js
{ $push: { <champ>: <valeur>, ... }}
 
db.hobbies.update({"_id": 1}, {$push: {"passions": "Sandra" }})
```

## L'operateur $addToSet

```js
db.personnes.find({"interets": "jardinage"})

// Les personnes qui ont pour interets jardinage puis bridge
db.personnes.find({"interets": ["jardinage", "bridge"]})

// Selectionne toutes les personnes qui ont pour intéret bridge et jardinage peut importe l'ordre
db.personnes.find({"interets": { $all : [ "bridge", "jardinage"]}})

// Toutes les personnes qui ont jardinage en deuxième position 
db.personnes.find({"interets.1": "jardinage"]})

// Toutes les personnes qui en 2 interets
db.personnes.find({"interets": {$size: 2}})

// Toutes les personnes qui ont au moins 2 interets
db.personnes.find({"interets.1": {$exists: 1}})
```

## L'operateur $elemMatch

```js
// Eleves qui ont une note entre 0 et 10
db.eleves.find({ "notes" : { $elemMatch: { $gt: 0, $lt: 10}}})

// Eleves qui ont comme note 5 ou 18,50 
db.eleves.find({ "notes": { $all : [5, 18.50] }})
```

# Les Tableaux de documents

```js
db.eleves.find({"notes.note": 10})
```

Renvoyer les documents dont les eleves ont au moins une note entre 10 et 15 dans une matiere quelconque:
```js
db.eleves.find({
	"notes" : {
		$elemMatch : {
			"note" : { $gt: 10, $lte: 15 }
		}
	}
})

db.eleves.find({ "notes.note": { $gt: 10, $lte: 15 }})

db.eleves.find({"notes.0.note": {$lt: 10}})
```

## Le tri

curseur.sort(<tri>)

# Pour exo :

- essayer de lever une erreur qui permet de verifier que les schemas de validation appliquer fonctionnent
- creer une collection
- la renomer
- la supprimer
- Faire la doc de 'findAndModify'