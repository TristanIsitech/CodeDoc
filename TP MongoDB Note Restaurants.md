# A. Préparation des données

## Question 1
Importez un jeu de données de localisation, comme les restaurants dans une ville. Vous pouvez utiliser la commande mongoimport pour importer des données dans MongoDB. 

```js
mongoimport --db=Restaurant --collection=business --file=yelp_academic_dataset_business.json --uri=mongodb+srv://Necr3w_OwnZ:<Password>@cluster0.gqlzpmk.mongodb.net
```

## Question 2 
Assurez-vous d'avoir un champ de localisation géospatiale, comme la latitude et la longitude.

```js
db.business.find({"latitude": 37.7817529521, "longitude": -122.39612197}, {"name": 1})
```

# B. Requêtes MongoDB

## Question 1
Recherchez les restaurants qui sont ouverts à partir de 18h00. Utilisez la méthode find () et les opérateurs de comparaison pour trouver les documents qui correspondent à vos critères. 

```js
// Les retaurants qui ouvre a 18h00 parmi tous les jours de la semaine
db.business.find({ 
	$or: [
		{"hours.Monday.open":{
			$regex: /^18:/ 
		}},
		{"hours.Tuesday.open":{
			$regex: /^18:/ 
		}},
		{"hours.Wednesday":{
			$regex: /^18:/ 
		}},
		{"hours.Thursday.open":{
			$regex: /^18:/ 
		}},
		{"hours.Friday.open":{
			$regex: /^18:/ 
		}},
		{"hours.Saturday.open":{
			$regex: /^18:/ 
		}},
		{"hours.Sunday.open":{
			$regex: /^18:/ 
		}}
	]
}, {"name": 1})

// Les retaurants qui ouvre a 8h un jour dans la semaine
db.business.aggregate([
	{ 
		$project: {
			name:1,
			ouverture: {
				$objectToArray : "$hours"
			} 
		}
	}, 
	{
		$match: {
			"ouverture.v.open":{
				$regex: /^18:/ 
			}
		}
	},
	{ 
		$project: {
			name:1
		}
	}
])
```

## Question 2
Triez les restaurants par note, du plus haut au plus bas. Utilisez la méthode sort () pour trier les résultats.

```js
db.Business.find({}, { "name": 1, "stars": 1, "_id": 0 }) .sort( { stars: -1 } )
```

# C. Indexation avec MongoDB

## Question 1
Créez un index sur le champ d'ouverture des restaurants pour améliorer les performances de la recherche. Utilisez la méthode createIndex (). 

```js
db.business.createIndex({ "hours": 1 }, { "name": "IndexHorraire" })

db.business.createIndex({ "hours.Monday.open": 1 }, { "name": "IndexOuvertureLundi" })
db.business.createIndex({ "hours.Tuesday.open": 1 }, { "name": "IndexOuvertureMardi" })
db.business.createIndex({ "hours.Wednesday": 1 }, { "name": "IndexOuvertureMercredi" })
db.business.createIndex({ "hours.Thursday.open": 1 }, { "name": "IndexOuvertureJeudi" })
db.business.createIndex({ "hours.Friday.open": 1 }, { "name": "IndexOuvertureVendredi" })
db.business.createIndex({ "hours.Saturday.open": 1 }, { "name": "IndexOuvertureSamedi" })
db.business.createIndex({ "hours.Sunday.open": 1 }, { "name": "IndexOuvertureDimanche" })


db.business.createIndex({
	"hours.Monday.open": 1,
	"hours.Tuesday.open":1,
	"hours.Wednesday":1,
	"hours.Thursday.open":1,
	"hours.Friday.open":1,
	"hours.Saturday.open":1,
	"hours.Sunday.open":1
}, {
	"name": "IndexOuvertureSemaine"
})
```

## Question 2
Vérifiez que l'index a été créé en utilisant la méthode listIndexes ().

```js
db.runCommand({ listIndexes: "business" }).cursor.firstBatch
```

# D. Requêtes géospatiales

## Question 1
Recherchez les restaurants qui se trouvent à moins de 2 km d'une certaine localisation. Utilisez la méthode find () avec un opérateur géospatial pour trouver les restaurants à l'intérieur d'un cercle. 

```javascript
db.business.createIndex( { lieu : "2dsphere" } )

db.business.find({ 
	lieu: { 
		$near: { 
			$geometry: { 
				type: "Point", 
				coordinates: [ <longitude>, <latitude> ]
				
			}, 
			$maxDistance: 2000 
		} 
	} 
}, {name: 1,  _id: 0, address: 1, city: 1})
```

## Question 2
Recherchez les restaurants qui se trouvent dans un certain rayon autour d'un point de localisation spécifique. Utilisez la méthode find () avec un opérateur géospatial pour trouver les restaurants à l'intérieur d'un cercle. 

```javascript
db.business.find({ 
	lieu: { 
		$near: { 
			$geometry: { 
				type: "Point", 
				coordinates: [ <longitude>, <latitude> ]
				
			}, 
			$maxDistance: <Nb_Km>
		} 
	} 
}, {name: 1,  _id: 0, address: 1, city: 1})
```

# E Framework d'agrégation

## Question 1
Calculez la moyenne des notes des restaurants. Utilisez le framework d'agrégation de MongoDB pour effectuer des calculs sur les données. 

```js
db.Business.aggregate([ { $group: { _id: null, moy: { $avg: "$stars" } } } ])
```

## Question 2
Trouvez les restaurants les plus populaires en fonction du nombre de commentaires. Utilisez le framework d'agrégation de MongoDB pour groupes les données et effectuer des calculs.

```js
db.Business.aggregate([ 
	{ $sort: 
	   { review_count: -1 } 
	},
	{ $limit: 20 },
	{ $project: 
		{ 
			_id: 0,
			name: 1,
			city: 1, 
			review_count: 1 
		}
	} 
])
```

# F. Export de la base de données

## Question 1
Exportez les résultats des requêtes dans un fichier CSV pour un usage ultérieur. Utilisez la commande mongoexport pour exporter des données de MongoDB.

```js
mongoexport --collection=business --db=Restaurant --out=restaurant.json --uri=mongodb+srv://Necr3w_OwnZ:<Password>@cluster0.gqlzpmk.mongodb.net
```