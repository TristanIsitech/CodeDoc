Requet GET 
https://pokeapi.co/api/v2/pokemon/{Pokemon_Name_Anglais}/
https://pokeapi.co/api/v2/pokemon/{Pokemon_Id}/

905 Pokemon au total
A!! Tous les pokemons n'ont pas de forme shiny

# Traitement :

#### Caractéristique
- Nom : name
- Id : id
- Xp de base : base_experience
#### Type
- Type Principal : types[0].type.name 
- Type Secondaire : type[1].type.name
#### Stats (hp / attack / defense / special-attack / special-defense / speed)
- Stats de vie : stats[0].stat.name // stats[0].base_stat
- Stats d'attack : stats[1].stat.name // stats[1].base_stat
- ...
#### Images
- Default : sprites.other.home.front_default
- Default : sprites.other.home.front_shiny
#### Evolution
??
