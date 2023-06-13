# Upit 3 - Za svaku kvalifikacionu trku, ispisati proizvođača čiji su vozači ostvarili najveći broj poena u zbiru. Prikazati ime trke, ime proizvođača i broj ostvarenih poena.

Kod upita:

~~~

db.getSiblingDB("formula1");
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

![Alt text](/v1/andrija/query_3/query_3_explain.png)

Primer izlaznog dokumenta:

![Alt text](/v1/andrija/query_3/query_3_output.png)
