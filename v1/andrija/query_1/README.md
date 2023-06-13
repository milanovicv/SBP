# Upit 1 - Pronaći i ispisati sve vozače koji su završili trku u top 5 pozicija, u kojoj su imali najduže vreme zadržavanja u pitu.

Kod upita:

~~~

db.getSiblingDB("formula1");
db.getCollection("pit_stops").aggregate(
    [
        {
            "$group" : {
                "_id" : {
                    "raceId" : "$raceId",
                    "driverId" : "$driverId"
                },
                "maxPitStop" : {
                    "$max" : "$milliseconds"
                }
            }
        }, 
        {
            "$sort" : {
                "_id.raceId" : NumberInt(1),
                "maxPitStop" : NumberInt(-1)
            }
        }, 
        {
            "$group" : {
                "_id" : "$_id.raceId",
                "driverId" : {
                    "$first" : "$_id.driverId"
                },
                "maxPitStop" : {
                    "$first" : "$maxPitStop"
                }
            }
        }, 
        {
            "$lookup" : {
                "from" : "driver_standings",
                "let" : {
                    "raceId" : "$_id",
                    "driverId" : "$driverId"
                },
                "pipeline" : [
                    {
                        "$match" : {
                            "$expr" : {
                                "$and" : [
                                    {
                                        "$eq" : [
                                            "$$raceId",
                                            "$raceId"
                                        ]
                                    },
                                    {
                                        "$eq" : [
                                            "$$driverId",
                                            "$driverId"
                                        ]
                                    },
                                    {
                                        "$lte" : [
                                            "$position",
                                            NumberInt(3)
                                        ]
                                    }
                                ]
                            }
                        }
                    }
                ],
                "as" : "result"
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
            "$group" : {
                "_id" : {
                    "raceId" : "$_id",
                    "driverId" : "$driver.driverId"
                },
                "driver" : {
                    "$first" : "$driver"
                },
                "maxPitStop" : {
                    "$first" : "$maxPitStop"
                }
            }
        }, 
        {
            "$project" : {
                "_id" : NumberInt(0),
                "raceId" : "$_id.raceId",
                "driver" : "$driver",
                "maxPitStop" : "$maxPitStop"
            }
        }
    ], 
    {
        "allowDiskUse" : false
    }
);


~~~
Rezultat Explain opcije:

![Alt text](/v1/andrija/query_1/query_1_explain.png)

Primer izlaznog dokumenta:

![Alt text](/v1/andrija/query_1/query_1_output.png)


