# Upit 2 (optimizovan) - Ispisi 3 najbolja vozača sa najvećim prosečnim brojem poena po trci kroz svaku sezonu.

Kod upita:

~~~

db = db.getSiblingDB("formula1-v2");
db.getCollection("most_points_drivers_per_season").aggregate([
  {
    $group: {
      _id: "$_id.season",
      topDrivers: {
        $push: {
          driverId: "$_id.driverId",
          driverName: "$_id.driverName",
          averagePointsPerRace: "$averagePointsPerRace"
        }
      }
    }
  },
  {
    $unwind: "$topDrivers"
  },
  {
    $sort: { "_id": 1, "topDrivers.averagePointsPerRace": -1 }
  },
  {
    $group: {
      _id: "$_id",
      top3Drivers: { $push: "$topDrivers" }
    }
  },
  {
    $project: {
      _id: 0,
      season: "$_id",
      top3Drivers: { $slice: ["$top3Drivers", 3] }
    }
  }
]);

~~~

Brzina izvršavanja: 0.028s