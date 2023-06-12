# Upit 5 - Prika�i najbolja 3 italijanska konstruktora u Formuli 1. U zapisu konstruktora je neophodno da budu voza?i, kao i ime konstruktora i ukupan broj poena koji su osvojili voza?i tih konstruktora.

Kod upita:

~~~

db = db.getSiblingDB("formula1");
db.getCollection("results").aggregate([
  {
    $lookup: {
      from: "drivers",
      localField: "driverId",
      foreignField: "driverId",
      as: "driver"
    }
  },
  {
    $unwind: "$driver"
  },
  {
    $lookup: {
      from: "constructors",
      localField: "constructorId",
      foreignField: "constructorId",
      as: "constructor"
    }
  },
  {
    $unwind: "$constructor"
  },
  {
    $project: {
      _id: 0,
      driverId: 1,
      driverName: { $concat: ["$driver.forename", " ", "$driver.surname"] },
      constructorName: "$constructor.name",
      constructorNationality: "$constructor.nationality",
      position: 1,
      points: 1
    }
  },
  {
      $match: {
          "constructorNationality": "Italian"
      }
  },
  {
    $group: {
      _id: {
        driverId: "$driverId",
        constructorName: "$constructorName"
      },
      driverName: { $first: "$driverName" },
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
])

~~~

Brzina izvr�avanja: 11.83s
