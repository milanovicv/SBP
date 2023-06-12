# Upit 2 - Ispisi 3 najbolja vozača sa najvećim prosečnim brojem poena po trci kroz svaku sezonu.

Kod upita:

~~~

db = db.getSiblingDB("formula1");
db.getCollection("driver_standings").aggregate([
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
        season: "$raceInfo.year",
        driverId: "$driverId"
      },
      totalPoints: { $sum: "$points" },
      totalRaces: { $sum: 1 }
    }
  },
  {
      $lookup: {
          from: "drivers",
          localField: "_id.driverId",
          foreignField: "driverId",
          as: "driver"
      }
  },
  {
    $unwind: "$driver"  
  },
  {
    $group: {
      _id: "$_id.season",
      topDrivers: {
        $push: {
          driverId: "$_id.driverId",
          driverName: { $concat: ["$driver.forename", " ", "$driver.surname"] },
          averagePointsPerRace: { $divide: ["$totalPoints", "$totalRaces"] }
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

Brzina izvr�avanja: 15.98s




