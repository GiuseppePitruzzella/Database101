# Trigger

Appunti di **Giuseppe Pitruzzella** - Corso di Database @ DMI, UniCt

## Definizione

SQL fornisce un costrutto estremamente potente, il *trigger*. I *trigger* rendono la base di dati in grado di reagire ad eventi definiti dall'amministratore. Una base di dati in cui sono definiti dei *trigger* si dice *attiva*.

I trigger sono stati introdotti solo a partire da SQL3 e sono basati sul paradigma Evento-Condizione-Azione (ECA). Ogni trigger viene *attivato* per uno specifico evento, viene *considerato* durante l'analisi del predicato al suo interno, ed *eseguito* se la condizione è soddisfatta.

## Sintassi e semantica di un Trigger in SQL3

* `CREATE TRIGGER NomeDelTrigger`

  ​	Ogni trigger possiede un *nome* attraverso cui può essere richiamato.

* `BEFORE | AFTER`

  Un trigger può essere *considerato* prima (`BEFORE`) o dopo (`AFTER`) l'evento. 

  Tipicamente un trigger di tipo `BEFORE` è utilizzato per condizionare l'esito di un operazione, quindi notare se quest'ultima deve essere effettivamente eseguita. 

  Un trigger di tipo `AFTER`, comunemente più utilizzato, viene *considerato* ed *eseguito* dopo l'evento ed è solitamente utilizzato per reagire alla modifica mediante opportune azioni.

* `[INSERT | DELETE | UPDATE [OF COL X]] ON TableName`

  Ogni trigger si riferisce ad un singola tabella (*TableName*), conosciuta anche con il nome "*target*". All'interno di quest'ultima è possibile svolgere un operazione per la manipolazione dei dati al suo interno, ovvero: `INSERT`, `DELETE`, `UPDATE` .

* `REFERENCING  {[OLD_TABLE AS OLDTABLEALIAS | NEW_TABLE AS NEWTABLEALIAS]} | {[OLD [ROW] AS OLDALIAS | NEW [ROW] AS NEWALIAS]}`

  Un trigger ha spesso la necessità di far riferimento allo stato precedente o successivo all'evento. A tale scopo è possibile, dopo aver definito il tutto secondo la sintassi sopra, utilizzare due *variabili* e due *tabelle di transizione*.

  [**NB**] Non sempre riferirsi a loro è sensato, come nel caso di `OLD` ed `OLD_TABLE` con `INSERT`, o nel caso di `NEW` e `NEW_TABLE` con `DELETE`. Attraverso `UPDATE` è possibile riferirsi sia a `NEW` che ad `OLD`.

* `FOR EACH [ROW | STATEMENT]`

  Nel caso di eventi che coinvolgano più tuple della tabella target, un trigger può essere attivato secondo una delle seguenti due modalità:

  - `FOR EACH ROW`, per eseguire il trigger su ogni tupla. E' utile (per es.) per controllare che tutti gli `UPDATE` siano legali, quindi in generale far sì che i vincoli di integrità siano rispettati. 

    Si noti che un trigger di tipo `BEFORE` *deve* essere anche di tipo `ROW`, non può richiamare altri trigger ed in generale, includere azioni che modifichino il database. All'utilizzo di `ROW` sono legate le variabili `OLD` o  `NEW` .

  - `FOR EACH STATEMENT`, per eseguire il trigger solo una volta per ogni comando che lo ha attivato.  E' utile (per es.) per controllare il numero di `INSERT` eseguito. All'utilizzo di `ROW` sono legate le variabili `OLDTABLE` o  `NEWTABLE` .

* `WHEN Condition`

  E' possibile, inoltre, specificare una condizione (*predicato*) nel trigger. Se il predicato è rispettato, una o più istruzioni SQL verrano eseguite. 

* `Istruzioni SQL`

  Le `Istruzioni SQL` rappresentano la parte finale di un trigger e possono distinguersi se il trigger è di tipo `BEFORE` o `AFTER`.

  Un trigger di tipo `BEFORE` può eseguire una delle tre azioni: 

  - `SELECT`;
  - `SIGNAL`, per annullare gli effetti dell'evento che ha attivato il trigger.
  - `SET`, per modificare uno o più campi di una nuova tupla.

  Un trigger di tipo `AFTER` può eseguire una delle cinque azioni: 

  - `SELECT`;
  - `INSERT`;
  - `DELETE FROM Tabella...`;
  - `UPDATE Tabella`;
  - `SIGNAL`;
  
  [**NB**] I trigger in SQL3 sono fortemente ispirati a DB2.

## Note sui Trigger

Ad ogni trigger è associato un *Trigger Execution Context* (**TEC**). L'attivazione di un trigger prevede quindi l'esecuzione del proprio TEC, il quale verrà temporaneamente sospeso se l'esecuzione del dato trigger causa l'attivazione di un nuovo trigger, quindi l'esecuzione del nuovo TEC, secondo un modello ricorsivo. Un eccessiva ricorsione potrebbe portare ad errore.

Si noti che se vi sono più trigger associati allo stesso evento (**trigger in conflitto**), i trigger di tipo *before* verrano eseguiti prima dei trigger di tipo *after*.

