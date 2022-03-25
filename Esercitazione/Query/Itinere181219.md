# Itinere 18 Dicembre 2019

**RICETTA** (<u>codice</u>, nome, istruzioni, *tipo*)
**TIPOLOGIA** (<u>id</u>, nome)
**ALIMENTO** (<u>id</u>, nome, costo, gruppo_alimentare)
**UTENSILE** (<u>id</u>, nome, quantita_disponibile)
**INGREDIENTI** (<u>ricetta, *alimento*</u>, quantita)
**USAUTENSILE** (<u>ricetta, *utensile*</u>, numero)

1. Identificare le chiavi primarie ed esterne dello schema;

2. Rispondere alle seguenti query in algebra relazionale:

   1. Per ogni ricetta trovare l’ingrediente usato in maggior quantità;
      $$
      R1 = R2 = INGREDIENTI \newline
      -- In\;R3\;memorizzo\;tutti\;gli\;elementi\;minori\;del\;massimo \newline
      R3 = R1 \Join_{R1.ricetta = R2.ricetta \and R1.alimento > R2.alimento \and R1.quantità < R2.quantità} R2 \newline
      R4 = \pi_{id, utensile}(R1) - \pi_{id, utensile}(R3) \newline
      R5 = \pi_{id, utensile} (R4)
      $$

   2. Trovare le tipologie di ricette che richiedono tutti gli utensili e tutti i cibi;
      $$
      R1 = UTENSILE \newline
      R2 = USAUTENSILE \newline
      R3 = \pi_{ricetta, utensile}(R2) \div \pi_{utensile} (\delta_{id \rightarrow utensile}(R1)) \newline
      R4 = \pi_{ricetta}(R3) \newline \newline
      
      R5 = ALIMENTO \newline
      R6 = INGREDIENTE \newline
      R7 = \pi_{ricetta, utensile}(R6) \div \pi_{alimento} (\delta_{id \rightarrow alimento}(R5)) \newline
      R8 = \pi_{ricetta}(R7) \newline \newline
      
      R9 = R4 \cap R8 \newline
      R10 = \pi_{Tipologia.ID}(R9 \Join_{R9.Tipo = Tipologia.ID} Tipologia)
      $$
      
   3. Trovare gli utensili che sono stati usati in tutte le ricette;
      $$
      R1 = \pi_{ricetta}(\delta_{codice \rightarrow ricetta}(RICETTA)) \newline
      R2 = \pi_{ricetta ,utensile}(USAUTENSILE) \newline
      R3 = R2 \div R1 \newline
      R4 = \pi_{utensili} (R3)
      $$
      
   
3. Rispondere alle seguenti query in SQL:

   1. Trovare le tipologie di ricette che hanno un costo di cibo **maggiore di quello medio** di tutte le
      ricette di tipo ‘NOUVELLE CUISINE’;

      ```sql
      
      ```
      
   2. Trovare le ricette che hanno usato tutti gli utensili;

      ```sql
      SELECT *
      ```
   
      

   3. Trovare le ricette che hanno usato utensili e quelle che non hanno usato utensili (scrivere
      solo una query piana);
   
4. Trigger:

   1. Supponendo l'esistenza di un attributo "*costo_totale*" nella relazione RICETTA e scrivere un trigger che, all’aggiunta di un ingrediente, aggiorni tale attributo.
      
      ```sql
      -- Nel caso in cui avremmo dovuto aggiungere, il che non è stato richiesto, 
      -- avremmo aggiunto una colonna a RICETTA mediante:
      -- ALTER TABLE RICETTA ADD nomeColonna DATATYPE
      CREATE TRIGGER AggiornaCosto
      AFTER INSERT ON INGREDIENTE
      FOR EACH ROW
      BEGIN
      	DECLARE CostoAlimento INT DEFAULT 0
      	SELECT Costo INTO CostoAlimento FROM ALIMENTO WHERE NEW.Alimento = ID
      	
      	UPDATE RICETTA SET COSTOTOTALE = COSTOTOTALE + CostoAlimento * NEW.Quantità
      	WHERE NEW.Ricetta = Codice
      END
      ```
      
      