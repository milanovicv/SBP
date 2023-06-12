# Upit 3 - Odredi za svaku stazu vozača koji je imao najveću prosečnu brzinu odvozanog jednog kruga.

Kod upita:

~~~

db = db.getSiblingDB("formula1");
db.getCollection("lap_times").aggregate([
  {
    $lookup: {
      from: "races",
      localField: "raceId",
      foreignField: "raceId",
      as: "raceInfo"
    }
  },
  {
    $unwind: "$raceInfo"
  },
  {
    $group: {
      _id: {
        circuitId: "$raceInfo.circuitId",
        driverId: "$driverId"
      },
      averageLapTime: { $avg: { $toDouble: "$milliseconds" } }
    }
  },
  {
    $sort: { "averageLapTime": 1 }
  },
  {
    $group: {
      _id: "$_id.circuitId",
      topDriver: { $first: { driverId: "$_id.driverId", averageLapTime: "$averageLapTime" } }
    }
  },
  {
    $lookup: {
      from: "drivers",
      localField: "topDriver.driverId",
      foreignField: "driverId",
      as: "driverInfo"
    }
  },
  {
    $unwind: "$driverInfo"
  },
  {
    $lookup: {
      from: "circuits",
      localField: "_id",
      foreignField: "circuitId",
      as: "circuitInfo"
    }
  },
  {
    $unwind: "$circuitInfo"
  },
  {
    $project: {
      _id: 0,
      circuitId: "$_id",
      circuitName: "$circuitInfo.name",
      topDriverName: { $concat: ["$driverInfo.forename", " ", "$driverInfo.surname"] },
      averageLapTime: "$topDriver.averageLapTime"
    }
  }
]);

~~~

Brzina izvršavanja: 264s
