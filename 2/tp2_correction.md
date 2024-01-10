## Correction

1. Quelles sont les 10 plus grandes chaines de restaurants (nom identique) ?
   - TOP 10 classique (2 façons de faire donc)
   ```
     db.restaurants.aggregate([
       { $sortByCount: "$name },
       { $limit: 10 }
     ])
   ```
1. Donner le Top 5 et le Flop 5 des types de cuisine, en terme de nombre de restaurants

   - idem, avec le tri qui change entre les 2 demandes

   ```
   db.restaurants.aggregate([
       { $sortByCount: "$cuisine },
       { $limit: 5 }
     ])

   db.restaurants.aggregate([
       { $sortByCount: "$cuisine },
       { $sort: { count: 1 }},
       { $limit: 5 }
   ])
   ```

1. Quelles sont les 10 rues avec le plus de restaurants ?
   - TOP 10 aussi
   ```
   db.restaurants.aggregate([{$sortByCount: "$address.street"}, {$limit: 10}])
   ```

1. Quelles sont les rues situées sur strictement plus de 2 quartiers ?
   - Essayez d'ajouter le nom des quartiers de chaque rue (cf `addToSet`)
   ```
   db.restaurants.aggregate([ 
    { $group: { 
      _id: "$address.street", 
      quartiers: { $addToSet: "$borough"}
    }}, 
    {$project: { 
      street: "$address.street", 
      nbQuartier: {$size: "$quartiers"}
    }}, 
    {$match: { nbQuartier: { $gt:2}}} ])
   ```

1. Lister par quartier le nombre de restaurants et le score moyen - Attention à bien découper le tableau `grades`
   ```
   db.restaurants.aggregate([ 
     { $unwind: "$grades" },
     { $group: { 
       _id: "$borough", 
       restaurant: { $addToSet: "$_id" },
       score: { $avg: "$grades.score" 
       }
      }},
      { $addToFields: { nb_restaurants: { $size: "$restaurant"}}},
      { $project: { restaurants: 0}}
     { $sort: { score: -1 }}
    ])
   ```

1. Donner les dates de début et de fin des évaluations

   - min et max sont dans un bateau

   ```
   db.restaurants.aggregate([ 
    { $unwind: "$grades" },
     { $group: { 
      _id: "$borough", 
      score: { $avg: "$grades.score" }, 
      debut: { $max: "$grades.date}, 
      fin: { $min: "$grades.date } 
     }},
     { $sort: { score: -1 }}
   ])
   ```

1. Quels sont les 10 restaurants (nom, quartier, addresse et score) avec le plus petit score moyen ?

   - découpage, regroupement par restaurant, tri et limite

   ```
     db.restaurants.aggregate([ 
      { $unwind: "$grades" },
      { $project: {
       name:1, borough:1, address:"$address.street"
      }}
      { $group: { _id: "$name", score: { $avg: "$grades.score" }}},
      { $match: { score: { $ne: null }}}
      { $sort: { score: 1 }},
      { $limit: 10}
    ])
   ```

1. Quels sont les restaurants (nom, quartier et addresse) avec uniquement des grades "A" ?

   - restriction à ceux qui ont A, découpage, suppression des autres grades que "A" et affichage des infos

   ```
    db.restaurants.aggregate([ 
      { $unwind: "$grades" },
      { $match: { "grades.grade": "A" }},
      { $group: { 
        _id: "$name",
        quartier: { $addToSet: "$borough"},
        adresse: { $addToSet: "$address.street" }
      }}
    ])    
   ```

   - on peut envisager d'autres choses (découpage, `addToSet`, et restriction à ceux pour lequel le tableau créé = ["A"] - par exemple)

   ```

   ```

1. Compter le nombre d'évaluation par jour de la semaine

   - petite recherche sur l'extraction du jour de la semaine à partir d'une date à faire

   ```
    db.restaurants.aggregate([ 
      { $unwind: "$grades" },
      { $group: {
        _id: { $dayOfWeek: "$grades.date" },
        nb: { $sum: 1 }
      }},
      { $sort: { _id: 1 } }
    ])
   ```

1. Donner les 3 types de cuisine les plus présents par quartier

   - simple à dire, compliqué à faire

   - une piste
     1. double regroupement à prévoir
     2. tri à prévoir
     3. regroupement avec `push`
     4. `slice` pour prendre une partie d'un tableau

````
  db.restaurants.aggregate([
    { $group: {
      _id: { quartier: "$borough", cuisine: "$cuisine"},
      count: { $sum: 1}
    }},
    { $sort: { count: -1}},
    { group: {
      _id: "$_id.quartier",
      cuisines: { $push: { cuisine: "$_id.cuisine", count: "$count"}}
    }},
    { $project: { _id: 1, cuisines: { $slice: [ "$cuisines", 3]}}}
  ])

````
