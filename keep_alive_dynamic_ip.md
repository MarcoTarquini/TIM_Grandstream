# NAT, keep alive e IP dinamico

Nella configurazione degli ATA dietro NAT, il keep-alive ha l'unico compito di evitare:
- Di dover aprire specifiche porte manualmente sul firewall.
- Che il mapping nelle tabelle di NAT scada.

**NOTA**: l'intervallo di keep alive non ha nulla a che fare con la scadenza della registrazione SIP, deve
essere impostato in base alla scadenza del mapping UDP del **proprio** router locale. Il server SIP non chiuderà
la connessione (e se è UDP non c'è nessuna "connessione" persistente, solo un IP:porta da contattare) fino a che
la registrazione non scade, ma non sarà più in grado di contattare l'ATA se il router NAT ha eliminato il mapping.
Una volta scaduto, una nuova registrazione può avvenire su una porta diversa.

Rimane il problema di indicare al proxy SIP remoto il proprio IP e la propria porta, nel caso di IP e porta dinamica.
Altrimenti le chiamate in ingresso non funzioneranno. I meccanismi sono sempre gli stessi:

- Usare un server STUN per ricavare l'IP e la porta lato WAN (se il NAT non è simmetrico)
- Usare received/rport negli header Via (se il proxy SIP li supporta)

Bisogna però impostare il Grandsteam come necessario:
- Per usare STUN: NAT traversal impostato a STUN, STUN server configurato
- Per usare Via: NAT traversal impostato a keep-alive, impostare *SIP REGISTER Contact Header Use* a WAN
- Meglio abilitare Symmetric RTP, così da poter usare la stessa porta RTP per inviare e ricevere òe chiamate.

**Nota:** non ho un Grandsteam con il quale provare, ma è possibile che *SIP REGISTER Contact Header Use* serva
anche con STUN. Se STUN ha problemi, provare non nuoce.

Fonte: https://fibra.click/voip/ (vabbé la damnatio memoriae, però...) e solo perché Technetium mi è simpatico...
