# Upit 3 (optimizovan) - Za svaku kvalifikacionu trku, ispisati proizvođača čiji su vozači ostvarili najveći broj poena u zbiru. Prikazati ime trke, ime proizvođača kao i broj ostvarenih poena.

Kod upita:

~~~

b = db.getSiblingDB("formula1-v2");
db.getCollection("refactored-qualifying").aggregate(
    [
        {
            "$group" : {
                "_id" : "$qualifyId",
                "maxPoints" : {
                    "$max" : "$standings.points"
                },
                "constructor" : {
                    "$first" : "$constructor.name"
                },
                "raceName" : {
                    "$first" : "$races.name"
                }
            }
        }, 
        {
            "$project" : {
                "_id" : NumberInt(0),
                "constructor" : NumberInt(1),
                "totalPoints" : "$maxPoints",
                "raceName" : NumberInt(1)
            }
        }
    ], 
    {
        "allowDiskUse" : false
    }
);

~~~

Rezultat Explain opcije:

![Alt text](/v2/andrija/query_3/query_3_explain.png)
