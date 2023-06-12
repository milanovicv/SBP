# Upit 5 (optimizovan) - Prikaži najbolja 3 italijanska konstruktora u Formuli 1. U zapisu konstruktora je neophodno da budu vozači, kao i ime konstruktora i ukupan broj poena koji su osvojili vozači tih konstruktora.

Kod upita:

~~~

db = db.getSiblingDB("formula1-v2");
db.getCollection("results").aggregate([
  {
      $match: {
          "constructor.nationality": "Italian"
      }
  },
  {
    $group: {
      _id: {
        driverId: "$driver.driverId",
        constructorName: "$constructor.name"
      },
      driverName: { $first: { $concat: ["$driver.forename", " ", "$driver.surname"] } },
      totalPoints: { $sum: "$points" }
    }
  },
  {
    $group: {
      _id: "$_id.constructorName",
      constructorName: { $first: "$_id.constructorName" },
      drivers: {
        $push: {
          driverId: "$_id.driverId",
          driverName: "$driverName",
          totalPoints: "$totalPoints"
        }
      },
      totalTeamPoints: { $sum: "$totalPoints" }
    }
  },
  {
    $sort: {
      "totalTeamPoints": -1
    }
  },
  {
    $limit : 3 
  },
  {
    $project: {
      _id: 0,
      constructorName: 1,
      totalTeamPoints: 1,
      drivers: 1
    }
  }
]);

~~~

Brzina izvršavanja: 0.044s