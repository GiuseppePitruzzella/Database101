# Veicolo

Esercizi svolti da **Giuseppe Pitruzzella** - Corso di *Database* @ DMI, UniCt

**VEICOLO**(<u>Targa</u>, Marca, Modello, Cilindrata)
**SINISTRO**(<u>Codice</u>, Descrizione)
**COINVOLGIMENTO**(<u>**Veicolo**, **Sinistro**, Data</u>, Luogo, ImportoDanno)

------

1. Determinare targa e marca dei veicoli coinvolti in sinistri a Milano con un danno superiore a 2000 euro.
   $$
   \pi_{Targa, Marca} (\sigma_{ImportoDanno >= 2000} (COINVOLGIMENTO) \Join_{Veicolo = Targa} Veicolo)
   $$
   

2. Determinare la targa dei veicoli che non sono stati coinvolti in sinistri nel 2013.
   $$
   R1 = \pi_{Veicolo} (\sigma_{Data >= 01.01.2013 \and Data <= 31.12.2013} (COINVOLGIMENTO)) \newline
   R2 = R1 - \pi_{Veicolo} (\delta_{Targa \rightarrow Veicolo} (VEICOLO))
   $$
   

   

3. Determinare marca e modello dei veicoli coinvolti in sinistri sia a Roma sia a Milano.
   $$
   R1 = \pi_{Veicolo} (\sigma_{Luogo = 'Milano'} (COINVOLGIMENTO)) \newline
   R2 = \pi_{Veicolo} (\sigma_{Luogo = 'Roma'} (COINVOLGIMENTO)) \newline
   R3 = R1 \cap R2
   $$

   ```sql
   -- Fai vedere al prof.
   SELECT C.Veicolo
   FROM COINVOLGIMENTO AS C
   WHERE C.Luogo = 'Milano' AND Veicolo IN (SELECT C1.Veicolo FROM COINVOLGIMENTO AS C1 WHERE C1.Luogo = 'Roma')
   ```

```sql
SELECT Veicolo
FROM Coinvolgimento
WHERE Luogo = 'Milano'
INTERSECT
SELECT Veicolo
FROM Coinvolgimento
WHERE Luogo = 'Roma'
```

