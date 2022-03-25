# Trigger 27 Febbraio 2020

**Persona**(CF, Nome, Cognome, DataDiNascita, Allergie, Patologie, DataProssimoVaccino, IDR)

**Vaccino**(IDV, Nome, Descrizione, EffettiCollaterali)

**Regione**(IDR, Nome)

**Da**(IDR, IDV)

**Richiamo**(IDV, Numero, Data1, Data2)

**Vaccinazione**(CF, IDV, Numero, Data, Ora, StatoSalute, Esito)

------

Implementare un trigger che dopo la vaccinazione, indica la prima data utile per il successivo vaccino pari a 60 giorni dopo la data attuale.

```sql
CREATE TRIGGER secondaDose
AFTER INSERT ON Vaccinazione
FOR EACH ROW
UPDATE Persona
SET DataProssimoVaccino += NEW.Data + 60
WHERE CF = NEW.CF
```
