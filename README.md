# Bar Baggerbeest reserveren

Reserveringssysteem voor Bar Baggerbeest op het Zeeburgereiland in Amsterdam. De ervaring moet net zo eigenzinnig zijn als de zaak: bar, pizza en streetfood, podium, cultuur en club in een product dat gasten snel helpt en het team overzicht geeft.

## Bedrijfsinformatie

- **Naam:** Bar Baggerbeest
- **Adres:** Rudi van Dantzigstraat 1, 1095 PK Amsterdam
- **Telefoon:** 020 - 308 00 13
- **E-mail:** proost@barbaggerbeest.nl
- **Website:** https://barbaggerbeest.nl/
- **Informatie:** https://barbaggerbeest.nl/over-bar-baggerbeest/
- **Locatie:** Zeeburgereiland / Sluisbuurt, Amsterdam

### Openingstijden

De openingstijden worden in productie beheerbaar gemaakt vanuit de adminomgeving. Gebruik op dit moment uitsluitend deze bevestigde basis:

- Dinsdag t/m donderdag: 14:00-23:00
- Vrijdag en zaterdag: 14:00-04:00
- Zondag: 14:00-23:00
- Keuken: dagelijks tot 21:00
- Vrijdag, zaterdag en zondag is er na 21:00 ruimte voor culturele programmering of privéfeesten.

Voor groepen vanaf 9 personen en volledige verhuur is persoonlijk maatwerk nodig.

## Huidige status

De huidige demo bestaat uit één zelfstandig bestand:

- `index.html` - klantgerichte reserveringspagina met styling en datuminteractie

De demo gebruikt tijdelijke externe Unsplash-afbeeldingen. De oorspronkelijke lokale fotomap was niet beschikbaar in deze workspace.

### Prototypebeperkingen

Dit is nog geen productieklare reserveringsapplicatie. Gegevens worden niet centraal opgeslagen en er is nog geen:

- database of echte beschikbaarheidscontrole;
- bescherming tegen dubbele reserveringen;
- authenticatie, autorisatie of rollenbeheer;
- echte tafelcapaciteit of tafeltoewijzing;
- e-mail-, sms- of WhatsApp-verzending;
- AVG/privacyflow voor productie.

Publiceer deze versie uitsluitend als demo. Neem geen echte klantreserveringen aan voordat backend, authenticatie en privacymaatregelen zijn toegevoegd.

## Productdoel

### Gastflow

Een gast moet:

1. datum, tijd, aantal personen en voorkeur kiezen: binnen, terras of bar;
2. een werkelijk beschikbare tijd selecteren;
3. naam, e-mail, telefoon en optionele notitie invullen;
4. de reservering direct bevestigen;
5. een bevestigingscode ontvangen;
6. vanaf 9 personen een groeps- of feestaanvraag indienen;
7. bevestiging, wijziging of annulering ontvangen.

### Teamflow

Het team moet:

1. reserveringen per service bekijken;
2. filteren op status, tijd, aantal gasten, zone en tafel;
3. reserveringen bevestigen, wijzigen, annuleren en als no-show markeren;
4. tafels toewijzen en de tafelindeling bekijken;
5. gastnotities en allergieën veilig kunnen zien;
6. vanuit het dashboard een bericht sturen;
7. groepsaanvragen opvolgen;
8. bezetting, covers, no-shows en piekmomenten analyseren;
9. openingstijden, sluitingsdagen, events en tafelcapaciteit beheren.

## Ontwikkelworkflow

### Fase 0 - audit en scope

1. Inspecteer de bestaande drie bestanden en documenteer IDs en interacties.
2. Behoud het klant- en dashboardconcept.
3. Vervang demo-opslag stapsgewijs door een backend.
4. Bouw eerst een betrouwbare MVP; voeg geen betaal- of marketingfunctionaliteit toe.

### Fase 1 - visuele identiteit

Behoud de premium donkere basis met deze design tokens:

- Lime: `#dfff45`
- Roze: `#fc7966`
- Paars: `#b68cf9`
- Warme off-white tekst
- Bijna zwart: `#090d0b`

De interface mag uitgesproken en cultureel aanvoelen, maar moet scanbaar blijven tijdens een drukke service.

### Fase 2 - betrouwbare MVP

De MVP bestaat uit een gastflow, een afgeschermd teamdashboard, centrale beschikbaarheid, statusbeheer en audit logging. Beschikbaarheid wordt altijd server-side berekend.

## Aanbevolen stack

- Frontend: bestaande HTML/CSS/JavaScript-prototype, daarna eventueel een componentlaag.
- Backend en database: Supabase met PostgreSQL.
- Authenticatie: Supabase Auth met rollen voor manager en team.
- Serverlogica: Supabase Edge Functions voor reserveren, notificaties en validatie.
- Hosting demo: GitHub Pages.
- Productie: frontend op een statische host of CDN, backend en database via Supabase.

Clientcode mag nooit rechtstreeks vertrouwen op beschikbaarheid, rolclaims of prijs-/capaciteitsregels. Kritieke controles horen in database policies, transacties en server-side functies.

## Datamodel

De exacte migraties worden tijdens de backendfase toegevoegd. Het minimale model bevat:

| Tabel | Doel |
| --- | --- |
| `profiles` | Teamprofielen, rollen en accountstatus |
| `guests` | Contactgegevens van gasten, met minimale bewaartermijn |
| `reservations` | Datum, tijd, covers, zone, status, notities en bevestigingscode |
| `tables` | Tafelnummer, zone, capaciteit en actieve status |
| `reservation_tables` | Koppeling tussen reserveringen en toegewezen tafels |
| `opening_hours` | Beheerbare standaardopeningstijden en keukentijden |
| `closures` | Sluitingsdagen en uitzonderingen |
| `events` | Culturele programmering en privéfeesten |
| `group_requests` | Aanvragen vanaf 9 personen of voor volledige verhuur |
| `messages` | Verzonden communicatie en bezorgstatus |
| `audit_logs` | Wie welke beheeractie uitvoerde en wanneer |

### Reserveringsstatussen

Gebruik een vaste enum of gecontroleerde waarden:

`pending` -> `confirmed` -> `completed`

Daarnaast zijn `cancelled`, `no_show` en `rejected` beschikbaar. Statuswijzigingen worden server-side gevalideerd en vastgelegd in `audit_logs`.

## Bedrijfsregels

- Reserveringen vallen binnen de beheerde openingstijden.
- De keuken sluit om 21:00; toon dit duidelijk bij tijden en bevestiging.
- Vrijdag, zaterdag en zondag kan na 21:00 een event of privéfeest de normale beschikbaarheid vervangen.
- Sluitingsdagen en events overschrijven de standaardopeningstijden.
- Het aantal gasten mag nooit boven de beschikbare tafelcapaciteit uitkomen.
- Een tafel mag niet dubbel geboekt worden binnen de ingestelde serviceduur.
- Combinaties van tafels mogen alleen als het team ze heeft toegestaan.
- Vanaf 9 personen start de groepsaanvraagflow in plaats van directe tafelreservering.
- Volledige verhuur is altijd een maatwerkaanvraag.
- Tijden, capaciteit, zones en uitzonderingen zijn beheerbaar; hardcode ze niet in de frontend.

## Communicatieflow

Voor productie wordt communicatie via een server-side provider verstuurd:

1. reservering ontvangen;
2. bevestiging of afwijzing versturen;
3. wijziging of annulering versturen;
4. optioneel herinnering versturen;
5. groepsaanvraag als taak aan het team aanbieden.

E-mail is de basis. SMS en WhatsApp zijn uitbreidingen met expliciete toestemming, providerconfiguratie en logging van bezorgstatus. Sla geen providergeheimen op in frontendcode.

## Dashboardvereisten

Het dashboard moet op een servicemoment vooral snel leesbaar zijn:

- dag- en serviceoverzicht;
- lijstweergave met status, tijd, naam, covers, zone en tafel;
- filters op status, tijd, covers, zone en tafel;
- detailpaneel met notities, allergieën en communicatiegeschiedenis;
- acties voor bevestigen, wijzigen, annuleren en no-show;
- tafelplan en capaciteitsoverzicht;
- groepsaanvragen en eventblokkades;
- rapportage voor bezetting, covers, no-shows en piekmomenten;
- beheer voor openingstijden, uitzonderingen, events en tafels.

## AVG en beveiliging

- Verzamel alleen gegevens die nodig zijn voor de reservering.
- Toon privacyinformatie en vraag noodzakelijke toestemming expliciet.
- Gebruik Supabase Row Level Security op alle klant- en reserveringstabellen.
- Beperk gastgegevens tot geautoriseerde teamrollen.
- Valideer invoer server-side en rate-limit publieke reserveringsacties.
- Gebruik niet-voorspelbare bevestigingscodes en laat gevoelige data niet in URLs lekken.
- Log beheeracties zonder onnodige persoonsgegevens te dupliceren.
- Definieer bewaartermijnen, verwijdering en inzage voordat productie live gaat.
- Gebruik secrets uitsluitend in server-side environment variables.

## Testplan

### Functioneel

- datum, tijd, zone en aantal personen kunnen worden gekozen;
- ongeldige of gesloten tijden worden geweigerd;
- beschikbare capaciteit wordt correct berekend;
- dubbele reserveringen worden atomair voorkomen;
- groepsaanvragen starten vanaf 9 personen;
- alle toegestane statusovergangen werken;
- berichten worden aan de juiste reservering gekoppeld.

### Dashboard

- rollen zien uitsluitend geautoriseerde gegevens;
- filters en servicewissel tonen consistente resultaten;
- tafeltoewijzing respecteert capaciteit en overlap;
- wijzigingen zijn terug te vinden in de auditlog.

### Kwaliteit

- mobiel, tablet en desktop;
- toetsenbordbediening en zichtbare focus;
- labels, foutmeldingen en statusupdates voor screenreaders;
- loading-, lege- en foutstatussen;
- timezone- en zomertijdscenario's voor Amsterdam;
- lokale tests, integratietests en end-to-end tests voor de kritieke boekingsflow.

## Deployment

### GitHub Pages demo

De huidige statische demo kan vanuit `main` op GitHub Pages worden gepubliceerd. Gebruik hiervoor geen echte persoonsgegevens of productiecredentials.

### Productie

1. Maak een aparte Supabase productieomgeving.
2. Voer database-migraties en RLS policies uit.
3. Configureer Auth, Edge Functions en notificatieproviders.
4. Stel environment variables en domeinen in.
5. Voer de volledige testset en een privacy/security review uit.
6. Monitor fouten, bezorgstatussen, dubbele boekingen en auditlogs.

## Prompt voor de volgende AI-tool

> Bouw de volgende MVP-fase van het reserveringssysteem voor Bar Baggerbeest in Amsterdam. Inspecteer eerst alle bestaande bestanden en behoud bestaande IDs en werkende interacties. Implementeer een Supabase/Postgres-backend met RLS, server-side beschikbaarheidscontrole, tafelcapaciteit, openingstijden, sluitingsdagen, events, groepsaanvragen vanaf 9 personen, reserveringsstatussen en een afgeschermd teamdashboard. Gebruik de bedrijfsinformatie en regels uit deze README als bron. Behandel de huidige frontend als demo: publiceer geen echte reserveringen zonder backend, authenticatie, privacyflow en dubbele-boekingsbeveiliging. Voeg gerichte tests toe voor beschikbaarheid, capaciteit, statusovergangen, rollen, accessibility en de gastflow. Werk in kleine stappen, valideer na iedere wijziging en documenteer aannames.

## Definition of Done

- Gast kan alleen werkelijk beschikbare tijden reserveren.
- Server-side validatie voorkomt dubbele boekingen en capaciteitsoverschrijding.
- Groepen vanaf 9 personen en volledige verhuur volgen de maatwerkflow.
- Openingstijden, keukenuren, sluitingsdagen, events en tafels zijn admin-beheerbaar.
- Teamleden kunnen reserveringen veilig bekijken, filteren en beheren.
- Rollen, RLS, rate limiting en audit logging zijn actief.
- Bevestiging, wijziging en annulering zijn getest en gekoppeld aan communicatie.
- AVG-informatie, bewaartermijnen en verwijderflow zijn vastgelegd.
- Kritieke gast- en dashboardflows hebben geautomatiseerde tests.
- De interface werkt op mobiel en desktop en voldoet aan basis-toegankelijkheid.
- Demo en productie zijn gescheiden; er staan geen secrets of echte persoonsgegevens in de frontend.
- Deployment, monitoring en rollbackprocedure zijn gedocumenteerd.

## Overzicht
Deze repository bevat een compacte, responsive landing page voor een vakantieverblijf genaamd Baggerbeest. De pagina bevat een hero-sectie, reserveringsformulier, verblijfsopties en een rustieke uitstraling voor een natuur- en waterervaring.

## Starten
Open de pagina rechtstreeks in een browser of serveer de map lokaal:

```bash
cd /workspaces/baggerbeest-reserveren
python3 -m http.server 8000
```

Ga daarna naar:

http://localhost:8000/

## Bestandsstructuur
- `index.html` – complete pagina met styling en kleine JavaScript-interactie

## Opmerking
De oorspronkelijke foto's uit het Windows-bestand waren niet beschikbaar in deze workspace. Daarom zijn tijdelijke externe Unsplash-afbeeldingen gebruikt zodat de pagina meteen werkend is.