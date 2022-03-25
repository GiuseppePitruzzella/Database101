# Itinere 08.12.2019

UTENTE(<u>ID</u>, Nome, Cognome, Classe)

PRODOTTO(<u>ID</u>, Nome, Descrizione, Categoria, TotaleVisitatori)

PUBLICIZZATO(<u>*IDProdotto, IDCliente, PaginaWEB*</u>, Data, Ora)

CLICCATO(<u>*IDProdotto, IDCliente, PaginaWEB*</u>, Data, Ora)

PAGINA(<u>ID</u>, DataInizioPubblicazione, DataFinePubblicazione, Titolo, Contenuto, Categoria, TotaleVisualizzazione)

VISPAGINA(<u>*PaginaWEB, IDCliente*, Data, Ora</u>)

------

### Algebra Relazionale

1. Trovare i prodotti pubblicizzati in **tutte** le pagine web.
   $$
   R1 = \delta_{id \rightarrow PaginaWEB} (\pi_{ID} (PAGINA)) \newline
   R2 = \pi_{IDProdotto, PaginaWEB}(PUBLICIZZATO) \newline
   R3 = R2 \div R1
   $$
   

2. Trovare i clienti che hanno cliccato nei prodotti di categoria "Salute" **e non** nei prodotti di categoria "Abbigliamento".
   $$
   R1 = \pi_{IDCliente}(\pi_{ID} (\sigma_{Categoria = Salute}(PRODOTTO)) \Join_{IDProdotto = ID} CLICCATO \newline
   R2 = \pi_{IDCliente}(\pi_{ID} (\sigma_{Categoria = Abbigliamento}(PRODOTTO)) \Join_{IDProdotto = ID} CLICCATO \newline
   R3 = R1 - R2
   $$
   

3. Indicare le pagine WEB che non hanno **mai** avuto click sui prodotti pubblicizzati.
   $$
   -- Trovo\;tutte\;le\;pagine\;WEB\;che\;hanno\;avuto\;almeno\;un\;click \newline
   R1 = \pi_{PaginaWEB}(PUBLICIZZATO \Join_{P.IDProdotto = C.IDProdotto \and P.PaginaWEB = C.PaginaWEB} CLICCATO) \newline
   -- Soluzione \newline
   R2 = \pi_{ID} (Pagina) - \delta_{PaginaWEB \rightarrow ID} (R1)
   $$

### SQL

1. Visualizzare il numero di prodotti publicizzati **e** cliccati in ogni pagina web.

   ```sql
   CREATE VIEW ProdottiPublicizzatiPerPagina (PaginaWEB, NumeroProdotti)AS
   SELECT PaginaWEB, COUNT(*)
   FROM PUBLICIZZATO
   GROUP BY PaginaWEB
   
   CREATE VIEW ProdottiCliccatiPerPagina (PaginaWEB, NumeroProdotti) AS
   SELECT PaginaWEB, COUNT(*)
   FROM PUBLICIZZATO
   GROUP BY PaginaWEB
   
   SELECT PP.PaginaWEB, PP.NumeroProdotti AS NPublicizzati, PC.NumeroProdotti AS NCliccati
   FROM ProdottiPublicizzatiPerPagina PP, ProdottiCliccatiPerPagina PC
   WHERE PP.PaginaWEB = PC.PaginaWEB
   ```

   

2. Trovare, per ogni data, la pagina WEB che ha ottenuto il numero **massimo** di click.

   ```sql
   CREATE VIEW NumeroClickPerData (Data, PaginaWEB, Numero) AS
   SELECT Data, PaginaWEB, COUNT(*)
   FROM CLICCATO
   GROUP BY Data, PaginaWEB
   
   SELECT T1.Data, T1.PaginaWEB
   FROM NumeroClickPerData T1
   WHERE Numero = (SELECT MAX(Numero) 
                   FROM NumeroClickPerData T2 
                   WHERE T2.Data = T1.Data)
   ```

   

3. Trovare le categorie di pagine WEB che hanno avuto publicizzate **tutte** le categorie di prodotti.

   ```sql
   SELECT *
   FROM PAGINA PA
   WHERE NOT EXISTS (SELECT DISTINCT CATEGORIA
                    FROM PRODOTTO PR
                    WHERE NOT EXISTS (SELECT *
                                     FROM PUBBLICIZZATO PB, PRODOTTO PR2 PAGINA PA2
                                     WHERE PB.IDProdotto = PR2.ID 
                                      AND PB.PaginaWEB = PA2.ID 
                                      AND PR2.Categoria = PR.Categoria 
                                      AND PA2.Categoria = PA.Categoria))
   ```

### Trigger

1. Implementare un trigger che nel momento in cui è aggiornato il valore DataFinePubblicazione nella relazione PAGINA, valorizzi automaticamente il campo totaleVisitatori (di PAGINA) 

   ```sql
   CREATE TRIGGER UpdateViews
   AFTER UPDATE OF DataFinePubblicazione ON Pagina
   FOR EACH ROW
   BEGIN
   	-- Trovo e salvo all'interno di una variaibile il numero di visualizzazioni.
   	DECLARE ViewsTotali INT DEFAULT 0 
   	SELECT COUNT(*) INTO ViewsTotali
   	FROM PAGINA
   	WHERE PaginaWEB = NEW.ID
   	
   	UPDATE PAGINA SET TotaleVisualizzazione = ViewsTotali 
   	WHERE ID = NEW.ID
   	
   	UPDATE PRODOTTO 
   	SET TotaleVisualizzazione = TotaleVisualizzazione + ViewsTotali 
   	WHERE ID IN (SELECT IDProdotto
                 FROM Pubblicizzato
                 WHERE PaginaWEB = NEW.ID);
   END
   ```

   

