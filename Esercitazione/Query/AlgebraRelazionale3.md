# Itinere 18 Dicembre 2019

**IMPIEGATO** (<u>matricola</u>, nome, cognome, **dipartimento**)

**DIPARTIMENTO** (<u>ID</u>, nome, sede)

**BENEFIT** (<u>ID</u>, nome, valore)

**GADGET** (<u>ID</u>, nome, tipo, valore)

**BENEFITASSEGNATO** (<u>matricola, **ID**</u>, data) 

**GADGETASSEGNATO** (<u>matricola, **ID**</u>, data)

- Identificare le chiavi primarie ed esterne dello schema;

- Rispondere alle seguenti query in *Algebra Relazionale*:

  - Trovare gli impiegati che nel 2018 non hanno avuto benefit ma hanno avuto gadget;
    $$
    % Tutte le persone che hanno ottenuto un benefit nel 2018 %
    R1 = \pi_{Matricola}(\sigma_{data \geq 01.01.2018 \and data \leq 31.12.2018}(BenefitAssegnato)) \newline
    % Tutte le persone che hanno ottenuto un gadget nel 2018 %
    R2 = \pi_{Matricola}(\sigma_{data \geq 01.01.2018 \and data \leq 31.12.2018}(GadgetAssegnato)) \newline
    % Cerco le persone che siano presenti all'interno di R2 ma non in R1 %
    R3 = R2 - R1
    % Dovrei stampare nome e cognome dell'impiegato? %
    $$
    
  - Trovare i dipendenti che hanno ricevuto tutti i gadget;
    $$
    \pi_{Matricola} (\pi_{Matricola, ID}(GadgetAssegnato) \div \pi_{ID} (Gadget)) \Join Impiegato
    $$
  
  - Trovare i gadget che sono stati dati a tutti i dipendenti;
    $$
    \pi_{ID}(\pi_{ID, Matricola}(GadgetAssegnato) \div \pi_{Matricola}(Impiegato)) \Join Gadget
    $$
    
