## Correction

1. Combien y-a-t'il de films en tout dans la base ?

```
db.movies.countDocuments()
```
1. Combien y-a-t'il de films en Français ?
```

db.movies.countDocuments({ languages: "French"})

```
1. Combien y-a-t'il de films franco-canadien ? (pays égal à France et Canada)
```

db.movies.countDocuments({ countries: "France"}, { countries: "Canada"})

```
1. Quels sont les genres (champs `genres`) existant dans la base ?
```

db.movies.aggregate([
  { $unwind: "$genres"},
  { $group: { _id: "$genres"}}
])

```
1. Retrouver toutes les informations du film dont le titre (champs `title` donc) est "Jurassic Park", en les affichant proprement (avec retour à la ligne et indentation)
```

db.movies.find({ title: "Jurassic Park"})

```
1. Retrouver tous les films ayant le mot "jurassic" dans leur titre (avec ou sans majuscule), et lister leur titre et l'année de sortie uniquement
```

db.movies.aggregate([
  { $match: { title: /jurassic park/i}},
  { $project: { title:1, released:1, _id:0}}
])

```
1. Donner uniquement les titres des films ayant une note de 3 sur le site IMDB (champs `rating` dans le champs `imdb`)
```

db.movies.aggregate([
  { $match: { "imdb.rating":3}}
])

```
1. Donner les titres des films et leur note IMDB, pour ceux dont elle est inférieure ou égale à 3
```

db.movies.aggregate([
  { $match: { "imdb.rating": {$lte:3}}},
  { $project: { title:1, rating: "$imdb.rating", _id:0}}
])

```
1. Donner la note du public Rotten Tomatoes moyenne (chanmps `rating` de `viewer` dans `tomatoes`) pour tous les films
```

db.movies.aggregate([ 
  { $project: { 
    title:1, 
    tomatoes_rating: "$tomatoes.viewer.rating", 
    _id:0 }}])

```
1. Donner par année (champs `year` donc) le nombre de films dans la base (en veillant à faire attention à l'ordre du résultat)
```

db.movies.aggregate([ 
  { $group: { 
    _id: "$year", 
    count: { $sum: 1}
  }}, 
  { $sort: { year: -1} }])

```
1. Donner par genre le nombre de films, en triant les genres par nombre de films décroissant
```

db.movies.aggregate([ 
  { $unwind: "$genres" }, 
  { $group: { 
    _id: "$genres", 
    count: { $sum: 1}
  }}, 
  { $sort: { count: -1} }])

```
1. Donner par genre la liste des types de films (champs `type`)
```

db.movies.aggregate([ 
  { $unwind: "$genres" }, 
  { $group: { 
    _id: "$genres", 
    count: { $sum: 1}
  }}, 
  { $sort: { count: -1} }])

```
1. Quels sont les 5 directeurs ayant dirigé le plus de films (nom du directeur, avec le nombre de films dirigés)
```
db.movies.aggregate([
  { $unwind: "$directors" },
  { $group: {
    _id: "$directors",
    nb_film: { $sum: 1 }
  }},
  { $sort: { nb_film: -1 }},
  { $limit: 5}
])
```
1. Combien y-a-t'il de directeurs dans la base ?
```
db.movies.distinct("directors").length
```
1. Donner par nombre d'acteurs, le nombre de films (attention, certains films n'ont pas d'acteurs, et donc pas le champs correspondant - On n'affichera pas le cas 0 acteurs)
```
db.movies.aggregate([
  { $match: { cast: { $exists: true}}},
  { $project: { 
    title:1,
    nb_acteur: { $size: "$cast"},
  }},
  { $group: {
    _id: "$nb_acteur",
    nb_film: { $sum: 1 }
  }},
  { $sort: { _id: 1 }}
])
```
