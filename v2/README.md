# Restruktuirana shema baze podataka

# Kolekcije

Pregled restruktuirane sheme baze podataka, koja je dobijena primenom šablona proširene reference i šablona aproksimacije.

Kolekcija RESULTS

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

Kolekcija BEST_CONSTRUCTORS_PER_SEASON

Polja: 
    • _id - identifikaciona oznaka, nested document sa poljima raceId i constructorId
    • raceYear - godina u okviru koje se desila trka
    • constructorName - naziv konstruktora
    • wins - broj pobeda


Kolekcija FASTEST_DRIVERS_ON_EACH_CIRCUIT

Polja: 
    • _id - identifikaciona oznaka, nested document sa poljima circuitId i driverId
    • averageLapTime - prosečno vreme odvozanog kruga
    • driverName - ime vozača
    • circuitName - ime staze

Kolekcija MOST_POINTS_DRIVERS_PER_SEASON

Polja: 
    • _id - identifikaciona oznaka, nested document sa poljima season, driverId, driverName
    • averagePointsPerRace - prosečan broj poena vožača na trci