# Inicijalna shema baze podataka

# Kolekcije

Pregled kolekcija iz baze podataka, kao i njihovih međusobnih odnosa.

Kolekcija CIRCUITS

Polja: 
    • circuitId - identifikaciona oznaka staze
    • circuitRef – skraćena oznaka 
    • name – ime staze
    • location – lokacija (mesto)
    • country - država
    • lat – geo. dužina
    • lng – geo. širina
    • alt - visina
    • url

Kolekcija CONSTRUCTOR_RESULTS

Polja: 
    • constructorResultsId – identifikaciona oznaka
    • raceId – referenca ka odgovarajućoj trci
    • constructorId – referenca ka odgovarajućem kontruktoru
    • points
    • status

Kolekcija CONSTRUCTOR_STANDINGS

Polja: 
    • constructorStandingsId – identifikaciona oznaka
    • raceId – referenca ka odgovarajućoj trci
    • constructorId – referenca ka odgovarajućem kontruktoru
    • points
    • position
    • positionText – string zapis
    • wins - pobede

Kolekcija  CONSTRUCTOR

Polja: 
    • constructorId – identifikaciona oznaka konstruktora
    • constructorRef – skraćen naziv
    • name – ime
    • nationality
    • url

Kolekcija DRIVER_STANDINGS

Polja: 
    • driverStandingsId – identifikaciona oznaka
    • raceId – referenca ka odgovarajućoj trci
    • driverId – referenca ka odgovarajućem vozaču
    • points
    • position
    • positionText – string zapis
    • wins - pobede

Kolekcija DRIVERS

Polja: 
    • driverId – identifikaciona oznaka vozača
    • driverRef – skraćena oznaka
    • number
    • code – kod pod kojim vozi trke
    • forename – ime vozača
    • surname – prezime vozača
    • dob – datum rođenja
    • nationalitz
    • url

Kolekcija LAP_TIMES

Polja: 
    • raceId – referenca ka odgovarajućoj trci
    • driverId – referenca ka odgovarajućem vozaču
    • lap – broj kruga
    • position
    • time
    • miliseconds – vreme u milisekundama

Kolekcija PIT_STOPS

Polja: 
    • raceId – referenca ka odgovarajućoj trci
    • driverId – referenca ka odgovarajućem vozaču
    • stop
    • lap
    • time
    • duration
    • miliseconds

Kolekcija QUALIFYING

Polja: 
    • qualifyId – identifikaciona oznaka
    • raceId – referenca ka odgovarajućoj trci
    • driverId – referenca ka odgovarajućem vozaču
    • constructorId – identifikaciona oznaka konstruktora
    • number
    • position
    • q1 – vreme u prvoj kvalifikacionoj trci
    • q2 – vreme u drugoj kvalifikacionoj trci
    • q3 – vreme u trećoj kvalifikacionoj trci

Kolekcija RACES

Polja: 
    • raceId – identifikaciona oznaka trke
    • year – godina kada se održala trka
    • round
    • circuitId – identifikaciona oznaka staze
    • name
    • date
    • time
    • url
    • fp1_date
    • fp1_time
    • fp2_date
    • fp2_time
    • fp3_date
    • fp3_time
    • quali_date
    • quali_time
    • sprint_date
    • sprint_time

Kolekcija RESULTS

Polja: 
    • resultsId – identifikaciona oznaka rezultata
    • raceId – referenca ka odgovarajućoj trci
    • driverId – referenca ka odgovarajućem vozaču
    • constructorId – identifikaciona oznaka konstruktora
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
    • statusId – referenca ka odgovarajućem statusu


Kolekcija SEASONS

Polja: 
    • year
    • url


Kolekcija SPRINT_RESULTS

Polja: 
    • resultsId – referenca ka result kolekciji
    • raceId – referenca ka odgovarajućoj trci
    • driverId – referenca ka odgovarajućem vozaču
    • constructorId – identifikaciona oznaka konstruktora
    • number
    • grid
    • position
    • positionText
    • positionOrder
    • points
    • laps
    • time
    • miliseconds
    • fastestLap – broj najbržeg kruga
    • fastestLapTime – vreme najbrže odvozanog kruga
    • statusId – referenca ka odgovarajućem statusu

Kolekcija STATUS

Polja: 
    • statusId – identifikaciona oznaka statusa
    • status

