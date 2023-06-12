# Upit 1 (optimizovan) - Za svaku sezonu navesti top 3 konstruktora koji su imali najviše pobeda.

Kod upita:

~~~

db = db.getSiblingDB("formula1-v2");
db.getCollection("best_constructors_per_season").aggregate([
  {
    $group: {
      _id: "$raceYear",
      topConstructors: {
        $push: {
          constructorId: "$_id.constructorId",
          constructorName: "$constructorName",
          wins: "$wins"
        }
      }
    }
  },
  {
    $project: {
      _id: 0,
      season: "$_id",
      topConstructors: {
        $slice: [
          {
            $filter: {
              input: "$topConstructors",
              cond: { $ne: ["$$this.wins", 0] }
            }
          },
          3
        ]
      }
    }
  }
]);

~~~

Brzina izvršavanja: 0.051s
