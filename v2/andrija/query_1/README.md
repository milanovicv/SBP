# Upit 1 - Za svaku trku, pronaći vozača, koji je završio u top 5 pozicija, a pritom je u toj trci imao najduže vreme zadržavanja u pit-u. Ispisati id trke, informacije o vozaču kao i maksimalno vreme zadržavanja.

Kod upita:

~~~

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

![Alt text](/v2/andrija/query_1/query_1_explain.png)

Upotreba indeksa:

![Alt text](/v2/andrija/query_1/query_1_index.png)

~~~
Zaključak:
  • Primećeno je usko grlo prilikom spajanja tabela, tako da se uvođenjem indeksa za raceId dobija potpuno optimizovan upit.
~~~