# Upit 4 (optimizovan)- Pronaći one vozače koji su bili više od 3 puta prvi na kraju , bilo kojeg, kruga trke koje su odvozali u karijeri.

Kod upita:

~~~

db = db.getSiblingDB("formula1-v2");
db.getCollection("refactored-races").aggregate(
    [
        {
            "$group" : {
                "_id" : {
                    "raceId" : "$raceId",
                    "driverId" : "$lap_time.driverId",
                    "lap" : "$lap_time.lap"
                },
                "position" : {
                    "$first" : "$lap_time.position"
                }
            }
        }, 
        {
            "$group" : {
                "_id" : {
                    "raceId" : "$_id.raceId",
                    "driverId" : "$_id.driverId"
                },
                "lapsInFirstPosition" : {
                    "$sum" : {
                        "$cond" : [
                            {
                                "$eq" : [
                                    "$position",
                                    NumberInt(1)
                                ]
                            },
                            NumberInt(1),
                            NumberInt(0)
                        ]
                    }
                }
            }
        }, 
        {
            "$match" : {
                "lapsInFirstPosition" : {
                    "$gte" : NumberInt(3)
                }
            }
        }, 
        {
            "$project" : {
                "_id" : NumberInt(0),
                "raceId" : "$_id.raceId",
                "driverId" : "$_id.driverId",
                "lapsInFirstPosition" : NumberInt(1)
            }
        }
    ], 
    {
        "allowDiskUse" : false
    }
);

~~~

Brzina izvršavanja: 2.4s
