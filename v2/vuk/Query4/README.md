# Upit 4 (optimizovan) - Napiši kratku statistiku o tome kakav učinak ima svako od vozača, na svim stazama na kojima je nastupao. Ispisati za svaku stazu koliko je poena na njoj osvojio, koliko je krugova odvozao i koju je maksimalnu brzinu dostigao. Takođe napisati i puno ime vozača.

Kod upita:

~~~

db = db.getSiblingDB("formula1-v2");
db.getCollection("results").aggregate([
  {
    $group: {
      _id: {
        driverId: "$driver.driverId",
        driverName: { $concat: ["$driver.forename", " ", "$driver.surname"] },
        circuitId: "$race.circuit.circuitId",
        circuitName: "$race.circuit.name"
      },
      totalPoints: { $sum: "$points" },
      totalLaps: { $sum: "$laps" },
      fastestLapSpeed: { $max: "$fastestLapSpeed" }
    }
  },
  {
    $group: {
      _id: {
          driverId: "$_id.driverId",
          driverName: "$_id.driverName"
      },
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
    $project: {
      _id: 0,
      driverId: "$_id.driverId",
      driverName: "$_id.driverName",
      circuitStats: 1
    }
  },
  {
    $sort: { "driverName": 1 }
  }
]);

~~~

Brzina izvršavanja: 0.228s