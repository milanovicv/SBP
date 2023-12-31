# Restruktuirana shema baze podataka

# Kolekcije

Pregled restruktuirane sheme baze podataka, koja je dobijena primenom šablona proširene reference i šablona aproksimacije.

Kolekcija RESULTS

~~~ db = db.getSiblingDB("formula1");
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
    $lookup: {
      from: "status",
      localField: "statusId",
      foreignField: "statusId",
      as: "stat"
    }
  },
  {
    $unwind: "$stat"
  },
  {
    $project: {
        _id: "$resultId",
        race: { raceId: "$race.raceId", name: "$race.name", circuit: { circuitId: "$circuit.circuitId", name: "$circuit.name" } },
        driver: { driverId: "$driver.driverId", forename: "$driver.forename", surname: "$driver.surname" },
        constructor: { constructorId: "$constructor.constructorId", name: "$constructor.name", nationality: "$constructor.nationality" },
        number: 1,
        grid: 1,
        position: 1,
        positionText: 1,
        points: 1,
        laps: 1,
        time: 1,
        miliseconds: 1,
        fastestLap: 1,
        rank: 1,
        fastestLapTime: 1,
        fastestLapSpeed: 1,
        stat: { statusId: "$stat.statusId", status: "$stat.status" }  
    }  
  },
  {
      $out: {
          db: "formula1-v2",
          coll: "results"
      }
  }
]);

Polja: 

    • resultsId – identifikaciona oznaka rezultata
    • race – nested document sa poljima raceId, name i circuit (dodatni nested document sa poljima circuitId, name)
    • driver - nested document sa poljima driverId, forename, surname
    • constructor - nested document sa poljima constructorId, name, nationality
    • grid
    • number
    • position
    • positionText
    • positionOrder
    • points
    • laps
    • time
    • miliseconds
    • fastestLap – broj najbržeg kruga
    • rank
    • fastestLapTime – vreme najbrže odvozanog kruga
    • fastestLapSpeed – najveća dostignuta brzina
    • stat - nested document sa poljima statusId, status

~~~

Kolekcija BEST_CONSTRUCTORS_PER_SEASON

~~~

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
      $lookup: {
          from: "constructors",
          localField: "constructorId",
          foreignField: "constructorId",
          as: "constructorInfo"
      }
  },
  {
    $unwind: "$raceInfo"  
  },
  {
    $project: {
        _id: "$constructorStandingsId",
        race: { raceId: "$raceId", year: "$raceInfo.year" },
        constructor: { constructorId: "$constructorId", name: "$constructorInfo.name" },
        position: "$position"
    }
  },
  {
      $group: {
          _id : {
              raceId: "$race.raceId",
              raceYear: "$race.year",
              constructorId: "$constructor.constructorId",
              constructorName: "$constructor.name"
          },
          wins: { $sum: { $cond: [ { $eq: ["$position", 1] }, 1, 0 ] } }
      }
  },
  {
      $project: {
          _id : {
              raceId: "$_id.raceId",
              constructorId: "$_id.constructorId"
          },
          raceYear: "$_id.raceYear",
          constructorName: "$_id.constructorName",
          wins: "$wins"
      }
  },
  {
      $out: {
          db: "formula1-v2",
          coll: "best_constructors_per_season"
      }
  }
]);

Polja: 

    • _id - identifikaciona oznaka, nested document sa poljima raceId i constructorId
    • raceYear - godina u okviru koje se desila trka
    • constructorName - naziv konstruktora
    • wins - broj pobeda

~~~

Kolekcija FASTEST_DRIVERS_ON_EACH_CIRCUIT

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
    $lookup: {
      from: "circuits",
      localField: "raceInfo.circuitId",
      foreignField: "circuitId",
      as: "circuitInfo"
    }
  },
  {
    $unwind: "$circuitInfo"
  },
  {
    $group: {
      _id: {
        circuitId: "$circuitInfo.circuitId",
        circuitName: "$circuitInfo.name",
        driverId: "$driverId"
      },
      averageLapTime: { $avg: { $toDouble: "$milliseconds" } }
    }
  },
  {
    $sort: { "averageLapTime": 1 }
  },
  {
    $lookup: {
      from: "drivers",
      localField: "_id.driverId",
      foreignField: "driverId",
      as: "driverInfo"
    }
  },
  {
    $unwind: "$driverInfo"
  },
  {
      $project: {
          _id: {
              circuitId: "$_id.circuitId",
              driverId: "$_id.driverId"
          },
          averageLapTime: "$averageLapTime",
          circuitName: "$_id.circuitName",
          driverName: { $concat: ["$driverInfo.forename", " ", "$driverInfo.surname"] }
      }
  },
  {
      $out: {
          db: "formula1-v2",
          coll: "fastest_drivers_on_each_circuit"
      }
  }
]);

Polja:

    • _id - identifikaciona oznaka, nested document sa poljima circuitId i driverId
    • averageLapTime - prosečno vreme odvozanog kruga
    • driverName - ime vozača
    • circuitName - ime staze

~~~

Kolekcija MOST_POINTS_DRIVERS_PER_SEASON

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
    $lookup: {
        from: "drivers",
        localField: "driverId",
        foreignField: "driverId",
        as: "driverInfo"
    }
  },
  {
    $unwind: "$driverInfo"  
  },
  {
      $group: {
        _id: {
          season: "$raceInfo.year",
          driverId: "$driverId",
          driverName: { $concat: ["$driverInfo.forename", " ", "$driverInfo.surname"] },
        },
        totalPoints: { $sum: "$points" },
        totalRaces: { $sum: 1 }
      }
  },
  {
      $project: {
        _id: {
          season: "$_id.season",
          driverId: "$_id.driverId",
          driverName: "$_id.driverName"
        },
        averagePointsPerRace: { $divide: ["$totalPoints", "$totalRaces"] }
      }
  },
  {
      $out: {
          db: "formula1-v2",
          coll: "most_points_drivers_per_season"
      }
  }
]);


Polja: 

    • _id - identifikaciona oznaka, nested document sa poljima season, driverId, driverName
    • averagePointsPerRace - prosečan broj poena vožača na trci

~~~

Kolekcija CONSTRUCTOR_QUALIFY_RACE_STANDINGS

~~~
db = db.getSiblingDB("formula1");
db.getCollection("qualifying").aggregate(
    [
        {
            "$lookup" : {
                "from" : "races",
                "localField" : "raceId",
                "foreignField" : "raceId",
                "as" : "races"
            }
        }, 
        {
            "$unwind" : {
                "path" : "$races"
            }
        }, 
        {
            "$lookup" : {
                "from" : "constructor_standings",
                "localField" : "races.raceId",
                "foreignField" : "raceId",
                "as" : "standings"
            }
        }, 
        {
            "$unwind" : {
                "path" : "$standings"
            }
        }, 
        {
            "$lookup" : {
                "from" : "constructors",
                "localField" : "standings.constructorId",
                "foreignField" : "constructorId",
                "as" : "constructor"
            }
        }, 
        {
            "$project" : {
    		"_id" : 0,
    		"qualifyId" : 1,
    		"raceId" : 1,
    		"driverId" : 1,
    		"constructorId" : 1,
    		"raceName": "$races.name",
    		"points": "$standings.points",
    		"constructorName": "$constructor.name",
            }
        }, 
        {
            "$out" : {
                "db" : "formula1-v2",
                "coll" : "refactored-qualifying"
            }
        }
    ], 
    {
        "allowDiskUse" : false,
        "hint" : "_id_"
    }
);

Polja:
    • qualifyId - identifikaciona oznaka kvalifikacije
    • raceId - identifikaciona oznaka trke
    • driverId - identifikaciona oznaka vozača
    • constructorId - identifikaciona oznaka proizvođača
    • points - broj ostvarenih poena
    • raceName - naziv trke
    • constructorName - naziv proizvođača

~~~

Kolekcija DRIVER_LAP_POSITIONS_PER_RACE

~~~

db = db.getSiblingDB("formula1");
db.getCollection("races").aggregate(
    [
        {
            "$lookup" : {
                "from" : "lap_times",
                "localField" : "raceId",
                "foreignField" : "raceId",
                "as" : "lap_time"
            }
        }, 
        {
            "$unwind" : {
                "path" : "$lap_time"
            }
        }, 
        {
            "$lookup" : {
                "from" : "drivers",
                "localField" : "lap_time.driverId",
                "foreignField" : "driverId",
                "as" : "driver"
            }
        }, 
        {
            "$unwind" : {
                "path" : "$driver"
            }
        }, 
        {
            "$project" : {
                "_id" : NumberInt(0),
                "raceId" : NumberInt(1),
                "lap" : "$lap_time.lap",
                "position" : "$lap_time.position",
                "driverId" : "$driver.driverId",
                "driverForename" : "$driver.forename",
                "driverSurname" : "$driver.surname"
            }
        }, 
        {
            "$out" : {
                "db" : "formula1-v2",
                "coll" : "refactored-races"
            }
        }
    ], 
    {
        "allowDiskUse" : false
    }
);



Polja:
    • raceId - identifikaciona oznaka trke
    • driverId - identifikaciona oznaka vozača
    • driverForename - ime vozača
    • driverSurname - prezime vozača
    • lap - broj kruga
    • position - pozicija vozača na kraju kruga

~~~

Kolekcija DRIVERS_FINISHED_RACES_PER_SEASON

~~~

db = db.getSiblingDB("formula1");
db.getCollection("results").aggregate(
    [
        {
            "$match" : {
                "statusId" : NumberInt(1)
            }
        }, 
        {
            "$lookup" : {
                "from" : "drivers",
                "localField" : "driverId",
                "foreignField" : "driverId",
                "as" : "driver"
            }
        }, 
        {
            "$lookup" : {
                "from" : "races",
                "localField" : "raceId",
                "foreignField" : "raceId",
                "as" : "race"
            }
        }, 
        {
            "$unwind" : {
                "path" : "$driver"
            }
        }, 
        {
            "$project" : {
                "_id" : NumberInt(0),
                "seasonYear" : "$race.year",
                "driverId" : NumberInt(1),
                "driverName" : {
                    "$concat" : [
                        "$driver.forename",
                        " ",
                        "$driver.surname"
                    ]
                }
            }
        }, 
        {
            "$out" : {
                "db" : "formula1-v2",
                "coll" : "refactored-results"
            }
        }
    ], 
    {
        "allowDiskUse" : false
    } 
);

Polja:
    • driverId - identifikaciona oznaka vozača
    • driverName - ime i prezime vozača
    • seasonYear - sezona

~~~

# Zaključak

Primetno je da operacije $lookup i $unwind, koje imaju ulogu u spajanju dokumenata iz različitih kolekcija, po referenci, produžavaju vreme izvršavanja upita značajno. S toga, prilikom optimizacije i restruktuiranja sheme, i ujedno primenom šablona proširene reference i šablona aproksimacije vreme izvršavanja se dosta smanjilo, pogotovo kod upita koji su bili višeminutni, a koji su se na kraju sveli red veličine nekoliko milisekundi. 
