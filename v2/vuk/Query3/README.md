# Upit 3 (optimizovan) - Odredi za svaku stazu vozača koji je imao najveću prosečnu brzinu odvozanog jednog kruga.

Kod upita:

~~~

db = db.getSiblingDB("formula1-v2");
db.getCollection("fastest_drivers_on_each_circuit").aggregate([
  {
    $group: {
      _id: {
          circuitId: "$_id.circuitId",
          circuitName: "$circuitName"
      },
      topDriver: { $first: { driverId: "$_id.driverId", driverName: "$driverName", averageLapTime: "$averageLapTime" } }
    }
  },
  {
    $project: {
      _id: 0,
      circuitId: "$_id.circuitId",
      circuitName: "$_id.circuitName",
      topDriverName: "$topDriver.driverName",
      averageLapTime: "$topDriver.averageLapTime"
    }
  }
]);

~~~

Brzina izvršavanja: 0.009s