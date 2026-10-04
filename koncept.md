# Projektbeskrivning – Distribuerad Kanban-applikation

## 1. Projektidé

Målet med projektet är att utveckla en enkel, webbaserad Kanban-applikation som kan användas av vår studiegrupp för att organisera uppgifter, följa arbetsflödet och samarbeta i realtid.

Applikationen ska bestå av ett centralt serversystem och separata klientapplikationer som körs lokalt på användarnas datorer. Alla klienter ansluter till den centrala servern via ett API för att läsa och uppdatera information på den gemensamma Kanban-tavlan.

Projektet är också ett tillfälle att praktiskt arbeta med HTML, CSS, JavaScript, Node.js, API-utveckling, autentisering och databashantering.

## 2. Systemarkitektur

Systemet delas upp i tre huvudsakliga delar:

- **Central server:** Hanterar klienter, autentisering, API-anrop och lagring av data.

- **Klientapplikationer:** Körs lokalt på varje användares dator och visar Kanban-tavlan i webbläsaren.

- **Administrationsgränssnitt:** Används för att registrera nya klienter, hantera åtkomst och administrera systemet.

Servern kommer att köras på en VPS med Node.js och Express. Eftersom servern redan har HTTPS kan kommunikationen mellan klienterna och servern krypteras.

## 3. Central server

Den centrala servern fungerar som systemets kärna. Den ansvarar för all kommunikation mellan klienterna och den gemensamma datalagringen.

Serverns huvudsakliga funktioner:

- Ta emot och behandla API-förfrågningar från klienterna.

- Autentisera klienter med hjälp av unika klient-ID och autentiseringstoken.

- Kontrollera klienternas behörigheter.

- Läsa och skriva Kanban-data.

- Hantera samtidiga transaktioner för att undvika konflikter vid uppdateringar.

- Administrera registrerade klienter.

Servern ska vara den enda komponenten som har direkt åtkomst till den gemensamma datalagringen. Klienterna ska endast kommunicera med servern via API.

## 4. Klientregistrering och autentisering

För att ansluta en ny användare till Kanban-systemet behöver klienten först registreras av en administratör på den centrala servern.

Vid registreringen skapas:

- Ett unikt klient-ID.

- En individuell autentiseringstoken.

- En åtkomstprofil som anger vilka funktioner klienten får använda.

- En status som visar om klienten är aktiv eller inaktiverad.

Klientens ID och token används för att identifiera och autentisera varje förfrågan till servern.

Administratören ska kunna skapa, visa status för och inaktivera registrerade klienter. Om en klient inte längre ska ha åtkomst ska dess token kunna återkallas.

Autentiseringsuppgifterna lagras lokalt i en konfigurationsfil på klientens dator och används när klientapplikationen startas. Token ska inte exponeras i webbläsarens JavaScript-kod.

## 5. Lokala klientapplikationer

Varje användare får samma klientapplikation, som körs lokalt med Node.js på den egna datorn.

När klienten startas ska den:

1. Läsa klientens lokala konfigurationsfil.

2. Initiera en lokal Node.js-process.

3. Starta ett lokalt webbgränssnitt.

4. Ansluta till den centrala servern via HTTPS.

5. Autentisera sig med sitt unika klient-ID och sin token.

6. Hämta och visa aktuell information från Kanban-tavlan.

Användaren arbetar med uppgifterna via ett webbgränssnitt i sin webbläsare. När användaren skapar, ändrar eller flyttar ett kort skickas en förfrågan till den centrala servern.

Klientapplikationen ska vara så enkel som möjligt och inte själv hantera den gemensamma databasen.

## 6. Datalagring

I den första versionen planerar vi att använda JSON-filer för att lagra informationen på servern.

Detta gör det möjligt att komma igång snabbt utan att behöva konfigurera ett separat databassystem.

JSON-filerna kan innehålla exempelvis:

- Registrerade klienter och deras behörigheter.

- Projekt och Kanban-tavlor.

- Uppgifter och beskrivningar.

- Uppgifternas status.

- Skapande- och uppdateringsinformation.

Autentiseringstoken ska lagras på ett säkert sätt, helst som hashvärden i stället för i klartext.

JSON-lösningen är tänkt som ett första steg för en mindre applikation. Om projektet växer kan vi senare övergå till en riktig databas, exempelvis SQLite eller MySQL.

## 7. Hantering av samtidiga transaktioner

Eftersom flera användare kan arbeta med samma Kanban-tavla måste systemet kunna hantera samtidiga uppdateringar.

För att undvika att två användare skriver till samma JSON-fil samtidigt kan servern använda en enkel låsningsmekanism.

När en klient påbörjar en skrivtransaktion låses den aktuella resursen tillfälligt. Om en annan klient försöker göra en samtidig uppdatering får den ett meddelande om att resursen är upptagen och kan försöka igen senare.

När transaktionen är slutförd frigörs låset så att nästa klient kan genomföra sin uppdatering.

Låsningen ska endast gälla under själva skrivoperationen och inte under hela tiden som användaren arbetar med applikationen.

För att minska risken för att äldre data skriver över nyare ändringar kan vi även införa versionshantering av data.

## 8. Säkerhet och anonymisering

En viktig del av arkitekturen är att begränsa mängden personuppgifter som skickas till servern.

Klienterna identifieras med unika ID i stället för att använda personliga uppgifter direkt i transaktionerna. På så sätt kan vi minska exponeringen av personuppgifter.

Kommunikationen mellan klienterna och servern sker via HTTPS, vilket krypterar informationen under överföringen.

Säkerheten ska även omfatta:

- Individuella autentiseringstoken för varje klient.

- Kontroll av behörigheter vid varje API-anrop.

- Säker lokal hantering av konfigurationsfiler.

- Möjlighet för administratören att återkalla åtkomst.

- Validering av data som skickas till servern.

Eftersom klient-ID fortfarande kan kopplas till registrerade användare bör informationen betraktas som pseudonymiserad snarare än helt anonymiserad.

## 9. Kanban-gränssnitt

Applikationens huvudsakliga funktion är en enkel och tydlig Kanban-tavla.

Tavlan kan exempelvis innehålla följande kolumner:

- **Att göra** – Uppgifter som ännu inte har påbörjats.

- **Pågår** – Uppgifter som för närvarande bearbetas.

- **Klart** – Uppgifter som är färdigställda.

Användarna ska kunna skapa nya kort, redigera befintliga uppgifter och flytta kort mellan kolumnerna.

Gränssnittet ska vara responsivt och fungera både på datorer och mindre skärmar.

## 10. Teknik och verktyg

| Teknik         | Användningsområde               |
| -------------- | ------------------------------- |
| HTML5          | Struktur för webbgränssnittet   |
| CSS3           | Design och responsiv layout     |
| JavaScript     | Interaktivitet i klienten       |
| Node.js        | Lokal klient och central server |
| Express.js     | API och serverhantering         |
| JSON           | Inledande datalagring           |
| HTTPS          | Krypterad kommunikation         |
| Git och GitHub | Versionshantering och samarbete |
| VPS            | Hosting av den centrala servern |

## 11. Utvecklingsplan

Projektet kan delas upp i flera steg för att göra utvecklingen enklare och mer överskådlig.

**Steg 1 – Prototyp**

- Skapa ett enkelt Kanban-gränssnitt med HTML, CSS och JavaScript.

- Implementera kort och kolumner.

- Testa gränssnittet lokalt.

**Steg 2 – Backend och API**

- Skapa en Node.js-server med Express.

- Utveckla API-endpoints för att läsa och uppdatera Kanban-data.

- Implementera JSON-baserad datalagring.

**Steg 3 – Klientanslutning**

- Skapa den lokala Node.js-klienten.

- Implementera konfigurationsfiler för klient-ID och token.

- Ansluta klienterna till den centrala servern via HTTPS.

**Steg 4 – Autentisering och administration**

- Skapa ett enkelt administrationsgränssnitt.

- Implementera klientregistrering och tokenhantering.

- Införa behörighetskontroll.

**Steg 5 – Transaktionshantering**

- Implementera låsning vid samtidiga skrivoperationer.

- Hantera konflikter och misslyckade transaktioner.

- Testa systemet med flera anslutna klienter.

**Steg 6 – Testning och vidareutveckling**

- Testa applikationen med flera användare.

- Förbättra säkerhet och användarupplevelse.

- Utvärdera behovet av att ersätta JSON med en databas.

## 12. Målsättning

Målet är att bygga en fungerande och enkel Kanban-applikation som kan användas av vår studiegrupp och samtidigt fungera som ett praktiskt utvecklingsprojekt.

Genom att arbeta med både frontend och backend får vi möjlighet att tillämpa våra kunskaper i webbutveckling och lära oss hur en distribuerad klient-server-applikation fungerar i praktiken.

Projektet ska utvecklas stegvis, med fokus på enkelhet, tydlig arkitektur, säker kommunikation och möjligheten att bygga vidare på systemet i framtiden.
