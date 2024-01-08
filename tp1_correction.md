## Correction

1. Donner les styles de cuisine présent dans la collection : 
   ```
   db.restaurants.distinct("cuisine")
   ```
1. Donner tous les grades possibles dans la base : 
   ```
   db.restaurants.distinct("grades.grade")`
1. Compter le nombre de restaurants proposant de la cuisine fraçaise ("French") : 
   ```
   db.restaurants.countDocuments({ cuisine: "French"})`
1. Compter le nombre de restaurants situé sur la rue "Central Avenue" : 
   ```
   db.restaurants.countDocuments({"address.street": "Central Avenue"})`
1. Compter le nombre de restaurants ayant eu une note supérieure à 50 : 
   ```
   db.restaurants.countDocuments({"grades.score": {$gt: 50}})`
1. Lister tous les restaurants, en n'affichant que le nom, l'immeuble et la rue : 
   ```
   db.restaurants.find({}, { name:1, "address.building":1, "address.street": 1})`
1. Lister tous les restaurants nommés "Burger King" (nom et quartier uniquement) : 
   ```
   db.restaurants.find({name: "Burger King"}, {name:1, borough:1, _id:0})`
1. Lister les restaurants situés sur les rues "Union Street" ou "Union Square" : 
   ```
   db.restaurants.find({$or: [ {"address.street": "Union Street"},{"address.street": "Union Square"} ]})`
1. Lister les restaurants situés au-dessus de la lattitude 40.90 : 
   ```
   db.restaurants.find({"address.coord.1": {$gt: 40.90}})`
1. Lister les restaurants ayant eu un score de 0 et un grade "A" : 
   ```
   db.restaurants.find({"grades.score": 0, "grades.grade": "A"})`

### Questions complémentaires

Nécessitent une recherche sur la toile pour compléter ce qu'on a déjà vu dans ce TP.

1. Lister les restaurants (nom et rue uniquement) situés sur une rue ayant le terme "Union" dans le nom : 
   ```
   db.restaurants.find({"address.street":/Union/}, {name:1, "address.street":1})`
1. Lister les restaurants ayant eu une visite le 1er février 2014 : 
   ```
   db.restaurants.find({"grades.date": ISODate("2014-02-14")})`
1. Lister les restaurants situés entre les longitudes -74.2 et -73.0 et les lattitudes 40.0 et 41.0 : 
   ```
   db.restaurants.find({"address.coord.0": {$gte: -74.2, $lte: -73.0}, "address.coord.1": {$gte: 40.0, $lte: 41.0}}, {name:1})`
