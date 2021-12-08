**UTENTE**(Codice, Nome, Cognome)
**GENERE**(Codice, Nome, Descrizione)
**VIDEO**(ID, **IDUtente**, Titolo, DataInserimento, Durata)
**GENEREVIDEO**(**IDVideo**, **IDGenere**)
**VISUALIZZAZIONI**(**IDVideo**, **IDUtente**, DataOra)


**Trovare il nome ed il cognome degli utenti che hanno caricato video di #tutti i generi.**
$$R1 = \pi_{IDUtente, IDGenere}(VIDEO \Join_{ID = IDVideo} GENEREVIDEO)$$
$$R2 = \pi_{IDGenere} (\delta_{Codice \rightarrow IDGenere} (GENERE))$$
$$R1 \div R2$$

```
SELECT *
FROM UTENTE U
WHERE NOT EXISTS (SELECT *
				FROM GENERE G
				WHERE G.Codice NOT IN (SELECT *
								FROM VIDEO V JOIN GENEREVIDEO GV ON V.ID = GV.IDVideo
								WHERE U.Codice = V.IDUtente AND GV.IDGenere = G.Codice))
```


**Trovare il nome ed il cognome degli utenti che #non hanno caricato video nel 2015**

$$(\pi_{IDUtente}(VIDEO) - \pi_{IDUtente} (\sigma_{DataInserimento >= 01.01.2015 AND DataInserimento <= 31.12.2015}(VIDEO))) \Join_{IDUtente = Codice} Utente$$

```
SELECT Nome, Cognome
FROM Utente
WHERE Codice NOT IN (SELECT IDUtente
					FROM VIDEO
					WHERE DataInserimento >= 01.01.2015 AND DataInserimento <= 31.12.2015)
```


**Identificare i video che hanno esattamente #due generi.**

$$R1 = R2 = GENEREVIDEO$$
$$R3 = R1 \Join_{R1.IDVideo = R2.IDVideo AND R1.IDGenere > R2.IDGenere} R2 $$
$$R4 = R3 \Join_{R3.IDVideo = GE.IDVideo AND R1.IDGenere > GE.IDGenere AND R2.IDGenere > GE.IDGenere} GENEREVIDEO GE$$
$$\pi_{IDVideo} (R3) - \pi_{IDVideo} (R4)$$

```
SELECT *
FROM GENEREVIDEO G0, GENEREVIDEO G1
WHERE G0.IDVideo = G1.IDVideo 
AND G0.IDGenere < G1.IDGenere
AND G0.IDVideo NOT IN (SELECT G2.IDVideo
						FROM GENEREVIDEO G2
						WHERE G0.IDVideo = G2.IDVideo AND 
						G0.IDGenere < G2.IDGenere AND
						G1.IDGenere < G2.IDGenere)
```