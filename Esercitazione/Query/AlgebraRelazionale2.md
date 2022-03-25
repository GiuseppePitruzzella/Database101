# Itinere 2016

**UTENTE**(<u>Codice</u>, Nome, Cognome)

**GENERE**(<u>Codice</u>, Nome, Descrizione)

**VIDEO**(<u>ID</u>, **IDUtente**, Titolo, DataInserimento, Durata)

**GENEREVIDEO**(<u>**IDVideo**</u>, <u>**IDGenere**</u>)

**VISUALIZZAZIONI**(<u>**IDVideo**</u>, <u>**IDUtente**</u>, <u>DataOra</u>)

------

1. Trovare il nome ed il cognome degli utenti che **non hanno** caricato video nel 2015.
   $$
   R1 = \pi_{IDUtente} (VIDEO) - \pi_{IDUtente} (\sigma_{DataInserimento >= 01.01.2015 \and DataInserimento <= 31.12.2015} (VIDEO)) \newline
   R2 = \pi_{Nome, Cognome} (R1 \Join_{Codice = IDUtente} Utente)
   $$
   
   
   
2. Trovare il nome ed il cognome degli utenti che hanno caricato video di **tutti** i generi.
   $$
   -- Codice \; di \; ogni \; genere \newline
   R1 = \delta_{Codice \rightarrow IDGenere} (\pi_{Codice} (Genere)) \newline \newline
   -- Tutte \; le \; relazioni \; tra \; l'utente \; ed \; il \; genere \; del \; video \; caricato \newline
   R2 = \pi_{Video.IDUtente, GenereVideo.IDGenere}(Video \Join_{ID = IDVideo} GenereVideo) \newline \newline
   -- Effettuo \; la \; divisione \; tra \; relazioni \; (x \; ,y) \; e \; totale \; (y)\newline
   R3 = R2 \div R1 \newline \newline
   -- Soluzione \newline
   R4 = \pi_{Nome, Cognome} (R3 \Join Utente)
   $$
   
3. Identificare i video che hanno **esattamente due** generi.
   $$
   R1 = R2 = R3 = GENEREVIDEO \newline
   R4 = \pi_{R1.IDVideo, R1.IDGenere, R2.IDGenere}(R1 \Join_{R1.IDVideo = R2.IDVideo \and R1.IDGenere <> R2.IDGenere} R2) \newline
   R5 = R4 \Join_{R4.IDVideo = R3.IDVideo \and R4.IDGenere <> R3.IDGenere \and } R3 \newline
   R6 = R5 - R4 \newline
   $$