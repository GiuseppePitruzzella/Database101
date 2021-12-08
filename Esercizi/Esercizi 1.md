
**VEICOLO**(**Targa**, Marca, Modello, Cilindrata) 
**SINISTRO**(**Codice**, Descrizione) 
**COINVOLGIMENTO**(**TargaVeicolo**, **Sinistro**, Data, Luogo, ImportoDanno)


**Determinare targa e marca dei veicoli coinvolti in sinistri a Milano con un danno superiore a 2000 euro**

	$$\pi_{Targa, Marca}(\sigma_{Luogo = 'Milano'\;AND\;Danno > 2000}(VEICOLO \Join_{Targa = TargaVeicolo} COINVOLGIMENTO)) $$
	
	```
	SELECT Targa, Marca
	FROM Veicolo JOIN Coinvolgimento ON Targa = TargaVeicolo
	WHERE ImportoDanno > 2000 AND Luogo = 'Milano'
	```

**Determinare la targa dei veicoli che #non sono stati coinvolti in sinistri nel 2013**

_Prendo tutti le targhe di coloro che sono stati coinvolti in sinistri nel 2013_
$$R1 = \pi_{TargaVeicolo}(\sigma_{Data >= 01.01.2013\;AND\;Data <= 31.12.2013}(COINVOLGIMENTO)) $$
_Calcolo il totale meno i coinvolti in sinistri nel 2013_
$$R2 = \pi_{TargaVeicolo} (COINVOLGIMENTO) - R1$$

```
SELECT TargaVeicolo
FROM COINVOLGIMENTO
WHERE TargaVeicolo NOT IN (SELECT TargaVeicolo
					FROM COINVOLGIMENTO
					WHERE Data >= 01.01.2013 AND Data <= 31.12.2013)
```


**Determinare marca e modello dei veicoli coinvolti in sinistri #sia a Roma sia a Milano**

_Determino tutte le informazioni per i veicoli coinvolti in sinistri a Roma_
$$R1 = \sigma_{Data = 'Roma'}(COINVOLGIMENTO \Join_{TargaVeicolo = Targa} VEICOLO)$$

_Determino tutte le informazioni per i veicoli coinvolti in sinistri a Milano_
$$R2 = \sigma_{Data = 'Milano'}(COINVOLGIMENTO \Join_{TargaVeicolo = Targa} VEICOLO)$$

_Effettuo l'intersezione tra le due viste_
$$R1 \cap R2$$

```
-- Soluzione 1
SELECT TargaVeicolo, Marca, Modello
FROM COINVOLGIMENTO JOIN VEICOLO ON TargaVeicolo = Targa
WHERE Luogo = 'Roma'
AND TargaVeicolo IN (SELECT TargaVeicolo
						FROM COINVOLGIMENTO
						WHERE Luogo = Milano)
						
-- Soluzione 2
SELECT TargaVeicolo, Marca, Modello
FROM COINVOLGIMENTO JOIN VEICOLO ON TargaVeicolo = Targa
WHERE Luogo = 'Roma'
INTERSECT
SELECT TargaVeicolo, Marca, Modello
FROM COINVOLGIMENTO JOIN VEICOLO ON TargaVeicolo = Targa
WHERE Luogo = 'Milano'
```
