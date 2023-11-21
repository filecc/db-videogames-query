SELECT

1- Selezionare tutte le software house americane (3)
```sql
SELECT * 
FROM `software_houses` 
WHERE `software_houses`.`country` = 'United States'
```

2- Selezionare tutti i giocatori della città di 'Rogahnland' (2)
```sql
SELECT * 
FROM `players` 
WHERE `players`.`city` = 'Rogahnland'
```

3- Selezionare tutti i giocatori il cui nome finisce per "a" (220)
```sql
SELECT * 
FROM `players` 
WHERE `players`.`name` LIKE '%a'
```

4- Selezionare tutte le recensioni scritte dal giocatore con ID = 800 (11)
```sql
SELECT * 
FROM `reviews`
WHERE `reviews`.`player_id` = '800'
```

5- Contare quanti tornei ci sono stati nell'anno 2015 (9)
```sql
SELECT id COUNT
FROM `tournaments`
WHERE `tournaments`.`year` = '2015'
```

6- Selezionare tutti i premi che contengono nella descrizione la parola 'facere' (2)
```sql
SELECT * 
FROM awards
WHERE awards.description LIKE '%facere%'
```

7- Selezionare tutti i videogame che hanno la categoria 2 (FPS) o 6 (RPG), mostrandoli una sola volta (del videogioco vogliamo solo l'ID) (287)
```sql
SELECT DISTINCT category_videogame.videogame_id
FROM category_videogame
WHERE category_videogame.category_id = 2
OR category_videogame.category_id = 6
```

8- Selezionare tutte le recensioni con voto compreso tra 2 e 4 (2947)
```sql
SELECT * 
FROM reviews
WHERE reviews.rating >= 2
AND reviews.rating <= 4
```
9- Selezionare tutti i dati dei videogiochi rilasciati nell'anno 2020 (46)
```sql
SELECT * 
FROM videogames
WHERE videogames.release_date >= Date('2020-01-01')
AND videogames.release_date <= Date('2020-12-31')
```
10- Selezionare gli id dei videogame che hanno ricevuto almeno una recensione da 5 stelle, mostrandoli una sola volta (443)
```sql
SELECT DISTINCT reviews.videogame_id 
FROM reviews 
WHERE reviews.rating > 4
```
*********** BONUS ***********

11- Selezionare il numero e la media delle recensioni per il videogioco con ID = 412 (review number = 12, avg_rating = 3.16 circa)
```sql
SELECT
COUNT(reviews.id) AS reviews_number,
AVG(reviews.rating)
FROM reviews 
WHERE reviews.videogame_id = '412'
```
12- Selezionare il numero di videogame che la software house con ID = 1 ha rilasciato nel 2018 (13)
```sql
SELECT *
FROM videogames
WHERE videogames.software_house_id = 1
AND videogames.release_date >= Date('2018-01-01')
AND videogames.release_date <= Date('2018-12-31')
```
------------------------------------------------------------------------------------------------------------------------------------------------------------

GROUP BY

1- Contare quante software house ci sono per ogni paese (3)
```sql
SELECT software_houses.country,
COUNT(software_houses.id)
FROM software_houses
GROUP BY software_houses.country
```
2- Contare quante recensioni ha ricevuto ogni videogioco (del videogioco vogliamo solo l'ID) (500)
```sql
SELECT reviews.videogame_id,
COUNT(reviews.id)
FROM reviews
GROUP BY reviews.videogame_id
```
3- Contare quanti videogiochi hanno ciascuna classificazione PEGI (della classificazione PEGI vogliamo solo l'ID) (13)
```sql
SELECT (category_videogame.category_id),
COUNT(category_videogame.videogame_id) AS videogames
FROM category_videogame
GROUP BY category_videogame.category_id
```
4- Mostrare il numero di videogiochi rilasciati ogni anno (11)
```sql
SELECT YEAR(videogames.release_date) AS Year,
COUNT(*) AS games_released
FROM videogames
GROUP BY YEAR(videogames.release_date)
ORDER BY Year
```
5- Contare quanti videogiochi sono disponbiili per ciascun device (del device vogliamo solo l'ID) (7)
```sql
SELECT (device_videogame.device_id),
COUNT(device_videogame.videogame_id) as videogames_count
FROM device_videogame
GROUP BY device_videogame.device_id
```
6- Ordinare i videogame in base alla media delle recensioni (del videogioco vogliamo solo l'ID) (500)
```sql
SELECT reviews.videogame_id,
COUNT(reviews.id),
AVG(reviews.rating) AS avg_rating
FROM reviews
GROUP BY reviews.videogame_id
ORDER BY avg_rating DESC
```
------------------------------------------------------------------------------------------------------------------------------------------------------------

JOIN

1- Selezionare i dati di tutti giocatori che hanno scritto almeno una recensione, mostrandoli una sola volta (996)
```sql
SELECT DISTINCT players.*
FROM players
JOIN reviews ON players.id = reviews.player_id
ORDER BY players.lastname
```
2- Sezionare tutti i videogame dei tornei tenuti nel 2016, mostrandoli una sola volta (226)
```sql
SELECT DISTINCT videogames.*
FROM videogames
JOIN tournaments ON tournaments.year = 2016
JOIN tournament_videogame ON tournament_videogame.videogame_id = videogames.id
```
3- Mostrare le categorie di ogni videogioco (1718)
```sql
SELECT videogames.name, videogames.id, categories.id AS category_id
FROM videogames
JOIN category_videogame ON videogames.id = category_videogame.videogame_id
JOIN categories ON category_videogame.category_id = categories.id
```
4- Selezionare i dati di tutte le software house che hanno rilasciato almeno un gioco dopo il 2020, mostrandoli una sola volta (6)
```sql
SELECT DISTINCT software_houses.*
FROM software_houses
JOIN videogames ON software_houses.id = videogames.software_house_id
WHERE videogames.release_date > '2020-01-01'
```
5- Selezionare i premi ricevuti da ogni software house per i videogiochi che ha prodotto (55)
```sql
SELECT software_houses.name, videogames.name, videogames.id, awards.name
FROM software_houses
JOIN videogames ON software_houses.id = videogames.software_house_id
JOIN award_videogame ON videogames.id = award_videogame.videogame_id
JOIN awards ON award_videogame.award_id = awards.id
```
6- Selezionare categorie e classificazioni PEGI dei videogiochi che hanno ricevuto recensioni da 4 e 5 stelle, mostrandole una sola volta (3363)
```sql
SELECT DISTINCT categories.name, pegi_labels.name
FROM categories
JOIN category_videogame ON categories.id = category_videogame.category_id
JOIN pegi_label_videogame ON category_videogame.videogame_id = pegi_label_videogame.videogame_id
JOIN pegi_labels ON pegi_label_videogame.pegi_label_id = pegi_labels.id
JOIN reviews ON pegi_label_videogame.videogame_id = reviews.videogame_id
WHERE reviews.rating IN (4,5)
```
7- Selezionare quali giochi erano presenti nei tornei nei quali hanno partecipato i giocatori il cui nome inizia per 'S' (474)
```sql
SELECT DISTINCT videogames.name, videogames.id
FROM videogames
JOIN tournament_videogame ON videogames.id = tournament_videogame.videogame_id
JOIN tournaments ON tournament_videogame.tournament_id = tournaments.id
JOIN player_tournament ON tournaments.id = player_tournament.tournament_id
JOIN players ON player_tournament.player_id = players.id
WHERE players.name LIKE 'S%'
```
8- Selezionare le città in cui è stato giocato il gioco dell'anno del 2018 (36)
```sql
SELECT DISTINCT tournaments.city
FROM tournaments
JOIN tournament_videogame ON tournaments.id = tournament_videogame.tournament_id
JOIN award_videogame ON tournament_videogame.videogame_id = award_videogame.videogame_id
JOIN awards ON award_videogame.award_id = awards.id
WHERE tournaments.year = 2018 
AND awards.name = "Gioco dell'anno"
```
9- Selezionare i giocatori che hanno giocato al gioco più atteso del 2018 in un torneo del 2019 (3306)
```sql 
SELECT DISTINCT players.id, players.name
FROM players
JOIN player_tournament ON players.id = player_tournament.player_id
JOIN tournaments ON player_tournament.tournament_id = tournaments.id
JOIN tournament_videogame ON tournaments.id = tournament_videogame.tournament_id
JOIN videogames ON tournament_videogame.videogame_id = videogames.id
JOIN awards ON awards.name = "Gioco più atteso"
WHERE tournaments.year = 2019
```

*********** BONUS ***********

10- Selezionare i dati della prima software house che ha rilasciato un gioco, assieme ai dati del gioco stesso (software house id : 5)
```sql
```
11- Selezionare i dati del videogame (id, name, release_date, totale recensioni) con più recensioni (videogame id : potrebbe uscire 449 o 398, sono entrambi a 20)
```sql
```
12- Selezionare la software house che ha vinto più premi tra il 2015 e il 2016 (software house id : potrebbe uscire 3 o 1, sono entrambi a 3)
```sql
```
13- Selezionare le categorie dei videogame i quali hanno una media recensioni inferiore a 2 (10)
