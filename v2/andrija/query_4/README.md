# Upit 4 (optimizovan)- Pronaći one vozače koji su bili više od 3 puta prvi na kraju kruga bilo koje trke na kojoj su učestvovali do sada u karijeri. Ispisati id vozača, id trke kao i broj krugova na kojima je vozač zauzeo prvo mesto.

Kod upita:

~~~

db = db.getSiblingDB("formula1-v2");
db.getCollection("refactored-races").aggregate(
    [
        {
            "$group" : {
                "_id" : {
                    "raceId" : "$raceId",
                    "driverId" : "$driverId",
                    "lap" : "$lap"
                },
                "position" : {
                    "$first" : "$position"
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

Rezultat explain opcije:

![Alt text](/v2/andrija/query_4/query_4_explain.png)
