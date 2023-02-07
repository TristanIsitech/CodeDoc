# Exercice 1

## Question
Affichez l’identifiant et le nom des salles qui sont des SMAC.

## Requête
```js
db.salles.find({"smac": true}, {"_id":1,"nom": 1})
```

## Retour du Shell
```js
{  
	_id: 1,  
	nom: 'AJMI Jazz Club'
},
{  
	_id: 2,  
	nom: 'Paloma'
}
```

# Exercice 2

## Quesstion
Affichez le nom des salles qui possèdent une capacité d’accueil strictement supérieure à 1000 places.

## Requête
```js
db.salles.find({"capacite":{$gt: 1000}}, {"_id":0,"nom": 1})
// On retourne le nom des salles qui ont plus de 1000 de capacite 
```

## Retour du Shell
```js
{  
	nom: 'Paloma'
}
```

# Exercice 3

## Question
Affichez l’identifiant des salles pour lesquelles le champ adresse ne comporte pas de numéro.

## Requête 
```js
db.salles.find({"adresse.numero": { $exists: false } }, { "_id": 1})
// On test si adresse.numero exist et on retourne seulement l'id de ceux qui n'ont pas de numéro
```

## Retour Shell
```js
{
	_id: 3
}
```

# Exercice 4

## Question
Affichez l’identifiant puis le nom des salles qui ont exactement un avis.

## Requête
```js
db.salles.find({"avis": { $size: 1 } }, { "_id": 1, "nom": 1})
// On récupère l'id et le nom de la salle qui comporte un tableau d'avis de taille 1
```

## Retour Shell
```js
{
	_id: 2,
	nom: 'Paloma' 
}
```

# Exercice 5

## Question
Affichez tous les styles musicaux des salles qui programment notamment du blues.

## Requête
```js
db.salles.find({"styles": { $eq: "blues" } }, { "_id": 0, "styles": 1})
// On récupère le tableau de styles des salles qui ont parmit leurs styles le 'blues' 
```

## Retour Shell
```js
{  
	styles: [    
		'jazz',    
		'soul',    
		'funk',    
		'blues'  
	]
},
{  
	styles: [    
		'blues',    
		'rock'  
	]
}
```

# Exercice 6

## Question
Affichez tous les styles musicaux des salles qui ont le style « blues » en première position dans leur tableau styles.

## Requête
```js
db.salles.find({"styles.0": { $eq: "blues" } }, { "_id": 0, "styles": 1})
// On fait la même requête que précendement mais on filter uniquement sur le première élément du tableau de styles
```

## Retour Shell
```js
{
	styles: [
		'blues',
		'rock'
	]
}
```

# Exercice 7

## Question
Affichez la ville des salles dont le code postal commence par 84 et qui ont une capacité strictement inférieure à 500 places (pensez à utiliser une expression régulière).

## Requête
```js
db.salles.find({"adresse.codePostal": { $regex: /^84/ }, "capacite": { $lt : 500 } }, { "_id": 0, "adresse.ville": 1})
// On sélectionne seulement les salles qui ont un code postal qui commence par 84 et une capacité strictement inférieur a 500 
```

## Retour Shell
```js
{  
adresse: { ville: 'Avignon' }
},
{  
adresse: { ville: 'Le Thor' }
}
```

# Exercice 8

## Question
Affichez l’identifiant pour les salles dont l’identifiant est pair ou le champ avis est absent.

## Requête
```js
db.salles.find({
	$or : [
	// On utilise $or pour selectionner des salles si au moins un des paramètres est vrai
		{
			"_id": { $mod : [2, 0] }
			// On test si l'id est pair en utilisant $mod pour faire un modulo 2
		}, 
		{
			"avis": { $exists: false }
			// On test si avis n'existe pas
		}
	]
}, 
{ "_id": 1}
)
```

## Retour Shell 
```js
{ _id: 2},
{ _id: 3}
```

# Exercice 9

## Question
Affichez le nom des salles dont au moins un des avis comporte une note comprise entre 8 et 10 (tous deux inclus).

## Requête
```js
db.salles.find({"avis.note" : { $gte: 8, $lte: 10}}, { "_id": 0, "nom": 1})
```

## Retour Shell
```js
{  nom: 'AJMI Jazz Club'},
{  nom: 'Paloma'}
```

# Exercice 10

## Question
Affichez le nom des salles dont au moins un des avis comporte une date postérieure au 15/11/2019 (pensez à utiliser le type JavaScript Date)

## Requête
```js
db.salles.find({"avis.date" : { $lt: new Date("2019-11-15")}}, { "_id": 0, "nom": 1})
```

## Retour Shell
```js
{  nom: 'AJMI Jazz Club'},
{  nom: 'Paloma'}
```

# Exercice 11

## Question
Affichez le nom ainsi que la capacité des salles dont le produit de la valeur de l’identifiant par 100 est strictement supérieur à la capacité.

## Requête
```javascript
db.salles.find({
  $expr: {
    $gt: [
      { $multiply: [ "$_id", 100 ] },
      "$capacite"
    ]
  }
}, {
  "nom": 1,
  "capacite": 1,
  "_id": 0
})
```

## Retour Shell

```javascript
{
    nom: 'Sonograf',
    capacite: 200
}
```

# Exercice 12

## Question
Affichez le nom des salles de type SMAC programmant plus de deux styles de musiques différents en utilisant l’opérateur $where qui permet de faire usage de JavaScript.

## Requête
```javascript
db.salles.find({
	$where: function() {
		return obj.styles.length > 2
})
```

## Retour Shell
```javascript

```

# Exercice 13

## Question
Affichez les différents codes postaux présents dans les documents de la collection salles.

## Requête
```javascript
db.salles.distinct("adresse.codePostal")
```

## Retour Shell

```javascript
[ '30000', '84000', '84250' ]
```

# Exercice 14

## Question
Mettez à jour tous les documents de la collection salles en rajoutant 100 personnes à leur capacité actuelle.

## Requête
```javascript
db.salles.updateMany({}, {$inc: {capacite: 100}})
```

## Retour Shell

```javascript
{
    acknowledged: true,
    insertedId: null,
    matchedCount: 3,
    modifiedCount: 3,
    upsertedCount: 0
}

```

# Exercice 15

## Question
Ajoutez le style « jazz » à toutes les salles qui n’en programment pas.

## Requête
```javascript
db.salles.updateMany({styles: {$ne: "jazz"}}, {$push: {styles: "jazz"}})
```

## Retour Shell

```javascript
{
    acknowledged: true,
    insertedId: null,
    matchedCount: 2,
    modifiedCount: 2,
    upsertedCount: 0
}
```

# Exercice 16

## Question
Retirez le style «funk» à toutes les salles dont l’identifiant n’est égal ni à 2, ni à 3.

## Requête
```javascript
db.salles.updateMany({_id: {$ne: 2}, _id: {$ne: 3}}, {$pull: {styles: "funk"}})
```

## Retour Shell

```javascript
{
    acknowledged: true,
    insertedId: null,
    matchedCount: 2,
    modifiedCount: 1,
    upsertedCount: 0
}
```

# Exercice 17

## Question
Ajoutez un tableau composé des styles «techno» et « reggae » à la salle dont l’identifiant est 3.

## Requête
```javascript
db.salles.updateOne({_id: 3}, {$addToSet: {styles: {$each: ["techno", "reggae"]}}})
```

## Retour Shell
```javascript
{
    acknowledged: true,
    insertedId: null,
    matchedCount: 1,
    modifiedCount: 1,
    upsertedCount: 0
}
```

# Exercice 18

## Question
Pour les salles dont le nom commence par la lettre P (majuscule ou minuscule), augmentez la capacité de 150 places et rajoutez un champ de type tableau nommé contact dans lequel se trouvera un document comportant un champ nommé telephone dont la valeur sera « 04 11 94 00 10 ».

## Requête
```javascript
db.salles.updateMany({nom: {$regex: /^P/i}}, {$inc: {capacite: 150}, $push: {contact: {telephone: "04 11 94 00 10"}}})
```

## Retour Shell
```javascript
{
    acknowledged: true,
    insertedId: null,
    matchedCount: 1,
    modifiedCount: 1,
    upsertedCount: 0
}
```

# Exercice 19

## Question
Pour les salles dont le nom commence par une voyelle (peu importe la casse, là aussi), rajoutez dans le tableau avis un document composé du champ date valant la date courante et du champ note valant 10 (double ou entier). L’expression régulière pour chercher une chaîne de caractères débutant par une voyelle suivie de n’importe quoi d’autre est [^aeiou]+$.

## Requête
```javascript
db.salles.updateMany(
   { nom: { $regex: /^[aeiou]+/i } },
   { $push: { avis: { date: new Date(), note: 10 } } }
)
```

## Retour Shell

```javascript
{
    acknowledged: true,
    insertedId: null,
    matchedCount: 1,
    modifiedCount: 1,
    upsertedCount: 0
}
```

# Exercice 20

## Question
En mode upsert, vous mettrez à jour tous les documents dont le nom commence par un z ou un Z en leur affectant comme nom « Pub Z », comme valeur du champ capacite 50 personnes (type entier et non décimal) et en positionnant le champ booléen smac à la valeur « false ».

## Requête
```javascript

```

## Retour Shell
```javascript

```

# Exercice 21

## Question
Affichez le décompte des documents pour lesquels le champ _id est de type « objectId ».

## Requête
```javascript
db.salles.find({_id: {$type: "objectId"}}).toArray().length;
```

## Retour Shell
```javascript
{
    1
}
```

# Exercice 22

## Question
Pour les documents dont le champ _id n’est pas de type « objectId », affichez le nom de la salle ayant la plus grande capacité. Pour y parvenir, vous effectuerez un tri dans l’ordre qui convient tout en limitant le nombre de documents affichés pour ne retourner que celui qui comporte la capacité maximale.

## Requête
```javascript
db.salles.find({"_id": {$not: {$type: "objectId"}}}).sort({capacite: -1}).limit(1).forEach(function(doc) { print(doc.nom); });
```

## Retour Shell
```javascript
{
    'Paloma'
}
```

# Exercice 23

## Question
Remplacez, sur la base de la valeur de son champ _id, le document créé à l’exercice 20 par un document contenant seulement le nom préexistant et la capacité, que vous monterez à 60 personnes.

## Requête
```javascript

```

## Retour Shell
```javascript

```

# Exercice 24

## Question
Effectuez la suppression d’un seul document avec les critères suivants : le champ _id est de type « objectId » et la capacité de la salle est inférieure ou égale à 60 personnes.

## Requête
```javascript
db.salles.deleteOne({ _id: { $type: "objectId" }, capacite: { $lte: 60 } })
```

## Retour Shell
```javascript
{
    acknowledged: true,
    deletedCount: 1
}
```

# Exercice 25

## Question
À l’aide de la méthode permettant de trouver un seul document et de le mettre à jour en même temps, réduisez de 15 personnes la capacité de la salle située à Nîmes.

## Requête
```javascript
db.salles.findOneAndUpdate( { ville: "Nîmes", nom: "Salle située à Nîmes" }, { $inc: { capacite: -15 } } );
```

## Retour Shell
```javascript
{
    null
}
```