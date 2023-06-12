# MongoDB projekat - predmet Sistemi baza podataka

Tema: Analiza skupa podataka 'Formula 1 World Championship (1950-2023)'

Autori: Vuk Milanović RA59/2019 i Andrija Stanišić RA55/2019

Opis skupa podataka

Podaci koji su uzeti u obzir tokom izrade projekta potiču sa sajta Kaggle. Konkretno, naziv dataset-a je 'Formula 1 World Championship (1950 - 2023)'. 
Dataset sadrži ppdatke ukratko o:

    • 850+ vozača
    • Preko pola miliona dokumenata koji se odnose na informacije o svakom odvozanom krugu u istoriji Formule 1
    • 1000+ trka
    • 200+ konstruktora
    • 25000+ dokumenata koji predstavljaju zabeležen rezultat svakog vozača na svakoj trci 

Sve zajedno podaci su raspoređeni na 14 kolekcija dokumenata.

# O realizaciji projekta

Inicijalna shema

Inicijalna shema baze podataka se nalazi u direktorijumu [Initial](v1/README.md), gde je kompletno objašnejno koje se to kolekcije nalaze u bazi, kao i međusobni odnosi istih.

Restruktuirana shema

Restruktuirana shema baze podataka se nalazi u direktorijumu [Restructured](v2/README.md).

Upiti

1. Za svaku sezonu navesti top 3 konstruktora koji su imali najviše pobeda.
2. Ispiši tri najbolja vozača sa nejvećim prosečnim brojem poena po trci kroz svaku sezonu.
3. Odredi za svaku stazu vozača koji je imao najveću prosečnu brzinu odvozanog jednog kruga.
4. Napiši kratku statistiku o tome kakv učinak ima svako od vozača, na svim stazama na kojima je nastupao. Ispisati za svaku stazu koliko je poena na njoj osvojio, broj krugova koji je odvozao i koju je maksimalnu brzinu dostigao. Takođe napisati i puno ime vozača.
5. Prikaži najbolja tri italijanska konstruktora u Formuli 1. U zapisu konsturktora je neophodno da budu vozači, kao i ime konstruktora, i ukupan broj poena koji su osvojili vozači tih konstruktora.

Implementacija

Implementacioni deo projekta se nalazi u glavnom direktorijumu.
