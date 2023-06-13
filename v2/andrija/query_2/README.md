# Upit 2 - Za svakog vozača odrediti koliko puta je napravio pit stop, za svaku trku u kojoj je osvojio neko od prva tri mesta. Ispisati ime i prezime vozača, sve trke na kojima je bio u top tri kao i vrednost sume.

Kod upita:

~~~

db.getSiblingDB("formula1");
db.getCollection("driver_standings").aggregate(
    [
        {
            "$match" : {
                "position" : {
                    "$lte" : NumberInt(3)
                }
            }
        }, 
        {
            "$lookup" : {
                "from" : "pit_stops",
                "localField" : "raceId",
                "foreignField" : "raceId",
                "as" : "pitStops"
            }
        }, 
        {
            "$unwind" : {
                "path" : "$pitStops"
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
                "_id" : "$driverId",
                "sumOfPitStops" : {
                    "$sum" : NumberInt(1)
                },
                "races" : {
                    "$addToSet" : "$race.name"
                }
            }
        }, 
        {
            "$lookup" : {
                "from" : "drivers",
                "localField" : "_id",
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
                "driverId" : "$_id",
                "driverName" : {
                    "$concat" : [
                        "$driver.forename",
                        " ",
                        "$driver.surname"
                    ]
                },
                "sumOfPitStops" : "$sumOfPitStops",
                "races" : NumberInt(1)
            }
        }
    ], 
    {
        "allowDiskUse" : false
    }
);

~~~

Rezultat Explain opcije:

![Alt text](/v2/andrija/query_2/query_2_explain.png)

Upotreba indeksa:

![Alt text](/v2/andrija/query_1/query_2_index.png)

~~~
Zaključak:
  • Primećeno je usko grlo prilikom spajanja tabela, tako da se uvođenjem indeksa za position polje, nad kojem se vrši match funkcija, dobija potpuno optimizovan upit.
~~~