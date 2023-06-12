# Upit 1 - Za svaku sezonu navesti top 3 konstruktora koji su imali najviše pobeda.

Kod upita

db = db.getSiblingDB("formula1");
db.getCollection("constructor_standings").aggregate([
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
    $project: {
        _id: 0,
        seasonYear: "$raceInfo.year",
        constructorId: "$constructorId",
        position: "$position"
    }  
  },
  {
    $group: {
      _id: {
        season: "$seasonYear",
        constructorId: "$constructorId"
      },
      wins: { $sum: { $cond: [ { $eq: ["$position", 1] }, 1, 0 ] } }
    }
  },
  {
    $sort: {
        "_id.season" : 1,
        "wins": -1
    }  
  },
  {
    $lookup: {
      from: "constructors",
      localField: "_id.constructorId",
      foreignField: "constructorId",
      as: "constructorInfo"
    }
  },
  {
    $unwind: "$constructorInfo"
  },
  {
    $group: {
      _id: "$_id.season",
      topConstructors: {
        $push: {
          constructorId: "$_id.constructorId",
          constructorName: "$constructorInfo.name",
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

Brzina izvršavanja: 5.74s
