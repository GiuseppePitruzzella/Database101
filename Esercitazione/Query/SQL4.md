# Esercizi SQL

## Esercitazione 1

**STADIO**(Nome, Citta, Capienza)

**INCONTRO**(NomeStadio, Data, Ora, Squadra1, Squadra2)

**NAZIONALE**(Paese, Continente, Categoria)

#### 1.1 Estrai i nomi degli stadi in cui non gioca nessuna nazionale europea.

```sql
SELECT S.Nome
FROM Stadio AS S
WHERE S.Nome NOT IN 
	(SELECT I.NomeStadio
  FROM Incontro
  WHERE S.Nome = I.NomeStadio AND Squadra1 NOT IN 
   	SELECT * FROM Nazionale WHERE Continente = 'Europa') 
  OR Squadra2 NOT IN 
  	SELECT * FROM Nazionale WHERE Continente = 'Europa')
  	
-- Seleziono i nomi degli stadi in cui nessuna partita è composta da squadre all'interno del continente Europeo.
```

[NB] E' possibile esprimere la stessa in Algebra Relazionale.

#### 1.2 Estrai la capienza complessiva degli stadi in cui si giocano le partire che hanno come prima squadra una nazione sudamericana.

```sql
SELECT SUM(Capienza)
FROM Stadio JOIN Incontro ON Nome = NomeStadio
WHERE Squadra1 IN 
	(SELECT Paese
  FROM Nazionale
  WHERE Continente = 'Sudamerica')
```

## Esercitazione 2

**MOTO**(<u>Targa</u>, Cilindrata, Marca, Nazione, Tasse)

**PROPRIETARIO**(<u>Nome, Targa</u>)

#### 2.1 Estrarre i nomi dei proprietari di solo moto giapponesi di almeno due marche diverse.

```sql
SELECT Nome
FROM Proprietario AS P JOIN MOTO AS M ON P.Nome = M.Nome
-- Rimuovo tutti i proprietari che si trovano all'interno della lista di proprietari di moto 		non giapponesi.
WHERE P.Nome NOT IN
	(SELECT Nome
  FROM Proprietario AS P1 JOIN Moto AS M1 ON P1.Targa = M1.Targa
  WHERE Nazione <> 'Giappone')
GROUP BY Nome -- Raggruppo per proprietario
-- Le marche distinte devono essere un numero maggiore o uguale a 2.
HAVING COUNT(DISTINCT Marca) >= 2 
```



