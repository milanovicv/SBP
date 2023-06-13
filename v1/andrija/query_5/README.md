# Upit 5 - Pronaći, za svaku sezonu, vozača koji je odvozao najviše uspešnih trka, odnosno trka koje kao rezultat imaju status "FINISHED". Ispisati informacije o vozaču kao i o broju odvozanih trka.

Kod upita:

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
            "$group" : {
                "_id" : {
                    "season" : "$race.year",
                    "driver" : "$driver.driverId"
                },
                "count" : {
                    "$sum" : NumberInt(1)
                }
            }
        }, 
        {
            "$group" : {
                "_id" : "$_id.season",
                "driver" : {
                    "$first" : "$_id.driver"
                },
                "maxFinishedRaces" : {
                    "$max" : "$count"
                }
            }
        }, 
        {
            "$lookup" : {
                "from" : "drivers",
                "localField" : "driver",
                "foreignField" : "driverId",
                "as" : "driverInfo"
            }
        }, 
        {
            "$unwind" : {
                "path" : "$driverInfo"
            }
        }, 
        {
            "$project" : {
                "_id" : NumberInt(0),
                "season" : "$_id",
                "driverName" : {
                    "$concat" : [
                        "$driverInfo.forename",
                        " ",
                        "$driverInfo.surname"
                    ]
                },
                "numOfFinished" : "$maxFinishedRaces"
            }
        }, 
        {
            "$sort" : {
                "season" : NumberInt(-1)
            }
        }
    ], 
    {
        "allowDiskUse" : false
    }
);

~~~

Rezultat Explain opcije:

![Alt text](/v1/andrija/query_5/query_5_explain.png)

Primer izlaznog dokumenta:

![Alt text](/v1/andrija/query_5/query_5_output.png)
