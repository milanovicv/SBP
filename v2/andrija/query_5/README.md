# Upit 5 (optimizovan)- Pronaći, za svaku sezonu, vozača koji je odvozao najviše uspešnih trka, odnosno trka koje kao rezultat imaju status FINISHED. Prikazati sezonu, ime vozača kao i broj uspešno odvozanih trka.

Kod upita:

~~~

db = db.getSiblingDB("formula1-v2");
db.getCollection("refactored-results").aggregate(
    [
        {
            "$group" : {
                "_id" : {
                    "season" : "$seasonYear",
                    "driverId" : "$driverId",
                    "driverName" : "$driverName"
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
                    "$first" : "$_id.driverId"
                },
                "maxFinishedRaces" : {
                    "$max" : "$count"
                },
                "driverName" : {
                    "$first" : "$_id.driverName"
                }
            }
        }, 
        {
            "$project" : {
                "_id" : NumberInt(0),
                "season" : "$_id",
                "driver" : "$driverName",
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
        "allowDiskUse" : false,
        "hint" : "_id_"
    }
);

~~~

Rezultat Explain opcije:

![Alt text](/v2/andrija/query_5/query_5_explain.png)


