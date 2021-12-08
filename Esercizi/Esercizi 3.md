**STUDENTE** (matricola, nome, cognome) 
**MATERIA** (id, titolo, descrizione) 
**ESERCIZI** (id, testo, soluzione, materia,numerosoluzioni)
**RISOLTO** (idesercizio, idstudente, data)

---

* Trovare le persone che #non hanno risolto esercizi di Database.
$$R1 = \pi_{Matricola}(\delta_{IDStudente \rightarrow Matricola}(\sigma_{M.Titolo = 'Database'}(RISOLTO \Join_{R.IDEsercizio = E.ID} ESERCIZI \Join_{E.Materia = M.ID} MATERIA)))$$
$$\pi_{Matricola} (STUDENTE) - R1$$

```
SELECT Matricola
FROM STUDENTE
WHERE S.Matricola NOT IN (SELECT IDStudente
		FROM RISOLTO R JOIN ESERCIZI E ON R.IDEsercizio = E.ID 
		JOIN MATERIA M ON E.Materia = M.ID
		WHERE Materia = 'Database')
```


* Trovare le materie per cui sono stati risolti #tutti gli esercizi

$$R1 = \pi_{Materia, IDEsercizio} (\delta_{ID \rightarrow IDEsercizio}(ESERCIZI \Join_{ID = IDEsercizio} RISOLTO)) $$
$$R2 = \delta_{ID \rightarrow Materia} (\pi_{ID} (MATERIA))$$
$$R1 \div R2$$

```
SELECT M.ID
FROM Materia M
WHERE NOT EXISTS 

(SELECT *
FROM (SELECT * FROM ESERCIZI E JOIN RISOLTO R ON R.IDEsercizio = E.ID) E
WHERE E.ID NOT IN 

(SELECT E.ID
FROM (SELECT * FROM ESERCIZI E JOIN RISOLTO R ON R.IDEsercizio = E.ID) E1
WHERE M.ID = E1.Materia AND E.ID = E1.ID))
```


* Per ogni materia #contare il numero di esercizi disponibili e quelli risolti (SQL).
```
SELECT Materia, E.EserciziDisponibili, R.EserciziRisolti
FROM

(SELECT Materia, COUNT(*) AS EserciziDisponibili 
FROM ESERCIZI E 
GROUP BY Materia) AS E 

JOIN

(SELECT Materia, COUNT(*) AS EserciziRisolti 
FROM ESERCIZI E JOIN RISOLTI ON IDEsercizio = ID 
WHERE NumeroSoluzioni <> 0 
GROUP BY Materia) AS R
ON E.Materia = R.Materia
```


* Trovare gli esercizi che #contengono la parola “SQL” che non sono stati risolti

```
SELECT *
FROM ESERCIZI E
WHERE NumeroSoluzioni = 0 AND Testo LIKE '%SQL%'
```