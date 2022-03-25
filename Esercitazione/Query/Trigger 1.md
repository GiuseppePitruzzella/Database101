# Esercitazione sui Trigger di UniBg

## Esercizio 1

* Si scrivano le regole attive che mantengono **aggiornato** il campo *NumeroCorsiAssegnati* in seguito ad ogni possibile modifica della tabella *Corso*.

​			**DOCENTE** ( CodiceFiscale, Nome, Cognome, DataDiNascita, Ruolo, NumeroCorsiAssegnati )
​			**CORSO** ( Codice-Corso, NomeCorso, NumeroOre, Docente, SedeErogazione )

```sql
CREATE TRIGGER NuovoCorso
AFTER INSERT ON CORSO
FOR EACH ROW
UPDATE DOCENTE
SET NumeroCorsiAssegnati = NumeroCorsiAssegnati + 1
WHERE CodiceFiscale = NEW.Docente

CREATE TRIGGER NuovoProfessore
AFTER UPDATE ON CORSO
FOR EACH ROW
BEGIN
UPDATE DOCENTE
SET NumeroCorsiAssegnati = NumeroCorsiAssegnati + 1
WHERE Docente = NEW.Docente

UPDATE DOCENTE
SET NumeroCorsiAssegnati = NumeroCorsiAssegnati - 1
WHERE Docente = OLD.Docente
END

CREATE TRIGGER RimozioneCorso
AFTER DELETE ON CORSO
FOR EACH ROW
UPDATE DOCENTE
SET NumeroCorsiAssegnati = NumeroCorsiAssegnati - 1
WHERE CodiceFiscale = NEW.Docente
```

## Esercitazione 2

​		**GIOCATORE** ( NumTessera, Nome, Squadra, Altezza, DataNascita, PresenzeInNazionale )

​		**SQUADRA** ( Nazione, Allenatore, NumPartiteVinte )

​		**PARTITA** ( IdPartita, Data, Squadra1, Squadra2, SetVintiSquadra1, SetVintiSquadra2, Arbitro )

​		**PARTECIPAZIONE** ( IdPartita, TesseraGiocatore, Ruolo, PuntiRealizzati )

* Costruire un trigger che mantenga aggiornato il valore di *NumPartiteVinte* di *Squadra* in seguito agli **inserimenti** in *Partita*, tenendo conto che *NumPartiteVinte* è relativo a tutta la storia della nazionale, non solo al campionato corrente, e che una squadra vince una partita quando vince 3 set.		

```sql
CREATE TRIGGER AggiornaPartiteVinte
AFTER INSERT ON PARTITA
FOR EACH ROW
UPDATE SQUADRA
SET NumPartiteVinte = NumPartiteVinte + 1
WHERE 
NEW.Squadra1 = Nazione AND SetVintiSquadra1 = 3
OR 
NEW.Squadra2 = Nazione AND SetVintiSquadra2 = 3
```

* Costruire, inoltre, un trigger che tenga aggiornato il numero delle presenze dei giocatori.

```sql
CREATE TRIGGER AggiornaPresenze
AFTER INSERT ON PARTECIPAZIONE
FOR EACH ROW
UPDATE GIOCATORE
SET PresenzeInNazionale = PresenzeInNazionale + 1
WHERE NEW.TesseraGiocatore = NumTessera
```

## Esercitazione 3

**CLIENTE** ( CodiceFiscale, nome, cognome, numTelefonico, PianoTariffario )

**PIANOTARIFFARIO** ( Codice, costoScattoAllaRisposta, costoAlSecondo ) 

**TELEFONATA** (CodiceFiscale, Data, Ora, numeroDestinatario, durata ) 

**BOLLETTA** (CodiceFiscale, Mese, Anno, importo )

* Scrivere un trigger che a seguito di ogni telefonata aggiorna la bolletta del cliente che ha chiamato

  ```sql
  CREATE TRIGGER AggiornaBolletta
  AFTER INSERT ON TELEFONATA
  FOR EACH ROW
  UPDATE BOLLETTA
  SET Importo = Importo + (SELECT P.CostoScatto + P.CostoSecondo * NEW.Durata
                           FROM Cliente C JOIN PianoTariffario P 
                           ON C.PianoTariffario = P.Codice
                          WHERE NEW.CodiceFiscale = C.CodiceFiscale)
  WHERE CodiceFiscale = NEW.CodiceFiscale
  AND Anno = NEW.Data.year
  AND Mese = NEW.Data.month
  ```

* Scrivere un trigger che alla fine di ogni mese (si supponga che la fine del mese sia segnalata dallʼevento END_MONTH) sconti dalle bollette 5 centesimi per ogni telefonata diretta a utenti della compagnia (cioè verso numeri di utenti registrati nella tabella Cliente) se lʼimporto complessivo della bolletta mensile supera i 100 euro.

  ```sql
  CREATE TRIGGER ScontoBolletta
  AFTER END_MONTH
  FOR EACH ROW
  UPDATE BOLLETTA B
  SET Importo = Importo - 0.5 * (SELECT COUNT(*) 
                                 FROM TELEFONATA T
                                 WHERE T.CodiceFiscale = B.CodiceFiscale 
                                 AND T.Data.month = ( sysdate() – 1 ).month
                                 AND T.Data.year = ( sysdate() – 1 ).year
                                 AND T.numeroDestinatario IN (SELECT NumTelefonico 
                                                                FROM Cliente)
  WHERE B.Importo > 100
  AND B.Mese = ( sysdate() – 1 ).month
  AND B.Anno = ( sysdate() – 1 ).year
  ```

  