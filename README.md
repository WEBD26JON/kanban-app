# kanban-app - Distribuerad Kanban-applikation

## Konceptet kortfattat

En enkel, distribuerad Kanban-applikation med en central Node.js-server och lokala klientapplikationer.

Servern hanterar autentisering, klientregistrering, API-kommunikation och datalagring i JSON. Varje klient körs lokalt med Node.js och ansluter till servern via HTTPS med ett unikt klient-ID och en autentiseringstoken.

Systemet använder transaktionslåsning för att förhindra samtidiga skrivkonflikter. Målet är att skapa ett enkelt, säkert och utbyggbart verktyg för samarbete och praktisk webbutveckling.

<img src="bilder/Kanban-appens systemarkitektur.png" width="720">
