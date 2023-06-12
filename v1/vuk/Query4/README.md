# Upit 4 - Napiši kratku statistiku o tome kakav učinak ima svako od vozača, na svim stazama na kojima je nastupao. Ispisati za svaku stazu koliko je poena na njoj osvojio, koliko je krugova odvozao i koju je maksimalnu brzinu dostigao. Takođe napisati i puno ime vozača.

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
      from: "races",
      localField: "raceId",
      foreignField: "raceId",
      as: "race"
    }
  },
  {
    $unwind: "$race"
  },
  {
    $lookup: {
      from: "circuits",
      localField: "race.circuitId",
      foreignField: "circuitId",
      as: "circuit"
    }
  },
  {
    $unwind: "$circuit"
  },
  {
    $group: {
      _id: {
        driverId: "$driver.driverId",
        circuitId: "$circuit.circuitId",
        circuitName: "$circuit.name"
      },
      totalPoints: { $sum: "$points" },
      totalLaps: { $sum: "$laps" },
      fastestLapSpeed: { $max: "$fastestLapSpeed" }
    }
  },
  {
    $group: {
      _id: "$_id.driverId",
      circuitStats: {
        $push: {
          circuitId: "$_id.circuitId",
          circuitName: "_id.circuitName",
          totalPoints: "$totalPoints",
          totalLaps: "$totalLaps",
          fastestLapSpeed: "$fastestLapSpeed"
        }
      }
    }
  },
  {
    $lookup: {
      from: "drivers",
      localField: "_id",
      foreignField: "driverId",
      as: "driverInfo"
    }
  },
  {
    $unwind: "$driverInfo"
  },
  {
    $project: {
      _id: 0,
      driverId: "$_id",
      driverName: { $concat: ["$driverInfo.forename", " ", "$driverInfo.surname"] },
      circuitStats: 1
    }
  },
  {
    $sort: { "driverName": 1 }
  }
]);

~~~

Brzina izvršavanja: 22.5s
