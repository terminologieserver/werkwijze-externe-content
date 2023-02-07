	# Tabellen en mappings Nederlands Huisarts Genootschap (NHG)
## Auteurs
__Geschreven door__: Chantal Schiltmeijer

__Review door__: 

## Inleiding
In dit document wordt de werkwijze omtrent het uploaden van de __NHG-tabellen en aanverwante resources zoals mappings__ gedocumenteerd. Het doel van dit document is inzichtelijk maken welke keuzes er gemaakt zijn voor dit proces, welke wijzigingen er ten opzichte van de bron gemaakt worden, en wat de aandachtspunten bij het gebruik van deze resources zijn.

## Methode
- De release files worden gedownload en omgezet in FHIR formaat volgens een geautomatiseerd proces.
- Bij het uploaden wordt een meta security label (https://www.hl7.org/fhir/security-labels.html) toegevoegd, waarmee toegang tot de resource beperkt kan worden tot een groep gebruikers. Dit label is voor NHG 'nhg*.read', waarbij de * vervangen wordt door het nummer van de tabel. Voor tabel 45 is het security label bijvoorbeeld 'nhg45.read'. Tevens wordt er een label toegevoegd waarmee een gebruiker toegang krijgt tot _alle_ NHG-tabellen: 'nhg.read'.
- Aan de codesystems wordt een valueSet key toegevoegd. Dit is een url voor een implicit valueset die het hele codesysteem omvat. Deze wordt gegenereerd volgens het format: {{url}}?vs. Met andere woorden: er wordt *?vs* achter de URL geplaatst. Hiermee is het mogelijk om de server aan te roepen met https://terminologieserver.nl/fhir/ValueSet/$expand?url={{url}}?vs , met alle mogelijke parameters voor een $expand request.

## De Nederlands Huisarts Genootschap (NHG) geeft vijf soorten licenties uit
### 1. Alle NHG-tabellen (nhg.read)
Licentiehouder heeft toegang tot alle tabellen die de NHG beschikbaar heeft gesteld
### 2. NHG-Tabel ICPC (nhgICPC.read)
Licentiehouder heeft toegang tot de ICPC-Thesaurus, en de volgende tabellen;
	- NHG-Tabel 24 - ICPC-1-NL
	- NHG-Tabel 27 - ICPC en contra-indicatie aarden
	- NHG-Tabel 50 - ICPC en attentiewaarde
	- NHG-Tabel 58 - ICPC en griepselectie
	- NHG-Tabel 65 - ICPC en patiëntvriendelijke titels
	- NHG-Tabel 72 - ICPC en profylaxe
	- NHG-Tabel 74 - ICPC en ongewenste groep
	- NHG-Tabel 75 - Behandelingen en griepselectie
	- NHG-Tabel 76 - ICPC en familie-anamnestisch gegeven
### 3. NHG-Tabel Diagnostische Bepalingen (nhgDB.read)
Licentiehouder heeft toegang tot de volgende tabellen;
	- NHG-Tabel 45 - Diagnostische bepalingen
	- NHG-Tabel 48 - Bepalingenclusters
	- NHG-Tabel 68 - Bepalingen nierfunctie
### 4. NHG-Tabel ICPC en NHG-Tabel Diagnostische bepalingen (combinatie van nhgICPC.read en nhgDB.read)
Licentiehouder heeft toegang tot de ICPC-Thesaurus, en de volgende tabellen;
	- NHG-Tabel 24 - ICPC-1-NL
	- NHG-Tabel 27 - ICPC en contra-indicatie aarden
	- NHG-Tabel 50 - ICPC en attentiewaarde
	- NHG-Tabel 58 - ICPC en griepselectie
	- NHG-Tabel 65 - ICPC en patiëntvriendelijke titels
	- NHG-Tabel 72 - ICPC en profylaxe
	- NHG-Tabel 74 - ICPC en ongewenste groep
	- NHG-Tabel 75 - Behandelingen en griepselectie
	- NHG-Tabel 76 - ICPC en familie-anamnestisch gegeven
	- NHG-Tabel 45 - Diagnostische bepalingen
	- NHG-Tabel 48 - Bepalingenclusters
	- NHG-Tabel 68 - Bepalingen nierfunctie
### 5. NHG-Tabellen Diagnostische Bepalingen + Contactwijze + Verrichtingen (combinatie van nhgDB.read en nhgCV.read)
Licentiehouder heeft toegang tot de volgende tabellen;
	- NHG-Tabel 45 - Diagnostische bepalingen
	- NHG-Tabel 48 - Bepalingenclusters
	- NHG-Tabel 68 - Bepalingen nierfunctie
	- NHG-Tabel 14 - Contactwijze

nb. readlabels voor de nieuwe licentiegroepen moeten nog worden ingericht.

## Beschikbare resources en aandachtspunten
### ConceptMaps
- Er is een mapping beschikbaar voor ICPC-1-NL naar SNOMED. Deze mapping is geproduceerd door Nictiz in samenwerking met het NHG. Niet alle codes zijn equivalente mappings: er is altijd een _comment_ toegevoegd voor interpretatie door een zorgverlener, waarin bij meervoudige mappings wordt uitgelegd welke code er in welke situatie gebruikt moet worden.
### Zoeken naar alle NHG resources
- CodeSystem / ValueSet / ConceptMap: Alle NHG-resources op de NTS zijn te vinden middels het gebruik van de parameter url:below = "https://referentiemodel.nhg.org/tabellen/".
### Algemene aandachtspunten
- Voor zover mogelijk zijn veelgebruikte kolomnamen gelijk getrokken over alle resources. Dit betekent dat de 3 varianten voor de opnameversie (__VO__ / __Versie opname__ / __versie opname__) worden vervangen door het meer beschrijvende __Versie opname__. Zo worden ook de verschillende varianten van mnemonics / memocodes (__Mnemo__ / __Mnem__ / __mnem__) vervangen door __Mnemo__. Iedere opgenomen kolomnaam wordt uitgeleverd als property welke met een hoofdletter begint.
- Wanneer een kolom leeg is in het originele release bestand (zoals bijvoorbeeld de kolom 'VV' bij actieve codes) wordt het property niet toegevoegd.
- Wanneer een regel aangeduid is als vervallen wordt de property __inactive__ toegevoegd met waarde __true__.
### Documentatie
- In onderstaand overzicht staan slechts de kenmerken/aandachtspunten beschreven die als aanvulling op de officiele documentatie van de tabellen gelden. Lees altijd de officiele documentatie (via https://referentiemodel.nhg.org/) voor gebruik.
### Aanvullende aandachtspunten per tabel
#### __NHG-Tabel 2 - Soort functie__
- VO / VM / VV zijn beschikbaar als properties, respectievelijk Versie opname / Versie mutatie / Versie vervallen.
- Kolom Mnem is beschikbaar als property Mnemo.
- Kolom Omschrijving is beschikbaar als display.
#### __NHG-Tabel 4 - Geslacht__
- VO / VM / VV zijn beschikbaar als properties, respectievelijk Versie opname / Versie mutatie / Versie vervallen.
- Kolom Mnem is beschikbaar als property Mnemo.
- Kolom Omschrijving is beschikbaar als display.
#### __NHG-Tabel 5 - Categorie patiënt__
- VO / VM / VV zijn beschikbaar als properties, respectievelijk Versie opname / Versie mutatie / Versie vervallen.
- Kolom Mnem is beschikbaar als property Mnemo.
- Kolom Omschrijving is beschikbaar als display.
#### __NHG-Tabel 6 - Burgerlijke staat__
- VO / VM / VV zijn beschikbaar als properties, respectievelijk Versie opname / Versie mutatie / Versie vervallen.
- Kolom Mnem is beschikbaar als property Mnemo.
- Kolom Omschrijving is beschikbaar als display.
#### __NHG-Tabel 9 - Reden vertrek__
- VO / VM / VV zijn beschikbaar als properties, respectievelijk Versie opname / Versie mutatie / Versie vervallen.
- Kolom Mnem is beschikbaar als property Mnemo.
- Kolom Omschrijving is beschikbaar als display.
#### __NHG-Tabel 12 - Soort derde__
- VO / VM / VV zijn beschikbaar als properties, respectievelijk Versie opname / Versie mutatie / Versie vervallen.
- Kolom Mnem is beschikbaar als property Mnemo.
- Kolom Omschrijving is beschikbaar als display.
#### __NHG-Tabel 14 - Contactwijze__
- VO / VM / VV zijn beschikbaar als properties, respectievelijk Versie opname / Versie mutatie / Versie vervallen.
- Kolom Mnem is beschikbaar als property Mnemo.
- Kolom Omschrijving is beschikbaar als display.
#### __NHG-Tabel 15 - Verrichtingen__
- In overleg met NHG nu geen prioriteit, en derhalve niet beschikbaar.
#### __NHG-Tabel 20 - Code opleiding__
- Kolom Mnem is beschikbaar als property Mnemo.
- Kolom Omschrijving is beschikbaar als display.
#### __NHG-Tabel 24 - ICPC-1-NL__
- De hiërarchie in de tabel is uit te lezen middels de parent/child properties.
- Kolom ICPC Code is beschikbaar als de code van het concept
- Kolom ICPC Titel is beschikbaar als de display van het concept
- De kolom ID is beschikbaar als property ID
#### __NHG-Tabel 25 - Gebruiksvoorschrift__
- VO / VM / VV zijn beschikbaar als properties, respectievelijk Versie opname / Versie mutatie / Versie vervallen.
- Kolom Mnem is beschikbaar als property Mnemo.
- Kolom BER is niet doorgevoerd in de FHIR resources, aangezien deze kolom niet meer in gebruik is.
- Tabel 25 is beschikbaar als een set CodeSystems:
    - Tekstcategorieën
    - Tijdseenheid
    - Aanvullende teksten
    - Gebruikseenheid
- De resource __Aanvullende teksten__ bevat een Coding property waarin verwezen wordt naar de resource __Tekstcategorie__.
#### __NHG-Tabel 27 - ICPC en contra-indicatie aarden__
- Waarde voor _automatisch_ wordt in comment geplaatst volgens format __Automatisch: {waarde}__
- De gebruikte target URI voor G-Standaard thesaurus 40 (http://z-index.nl/ns/CodeSystem/Thesaurus40) is nog niet definitief, en wijzigt waarschijnlijk op korte termijn.
#### __NHG-Tabel 42 - Aanduiding naamgebruik__
- VO / VM / VV zijn beschikbaar als properties, respectievelijk Versie opname / Versie mutatie / Versie vervallen.
- Kolom Omschrijving is beschikbaar als display.
#### __NHG-Tabel 43 - Familierelaties__
- VO / VM / VV zijn beschikbaar als properties, respectievelijk Versie opname / Versie mutatie / Versie vervallen.
- Kolom Omschrijving is beschikbaar als display.
#### __NHG-Tabel 45 - Diagnostische bepalingen__
- versie O / versie M / versie V zijn beschikbaar als properties, respectievelijk Versie opname / Versie mutatie / Versie vervallen.
- Tabel 45 is beschikbaar als een set resources. De tabel zelf is beschikbaar als CodeSystem, met een aantal hulptabellen waarnaar verwezen wordt in de properties van de concepten.
- Het bepalingnummer is beschikbaar als de code van het concept.
- De kolom omschrijving is beschikbaar als de display van het concept.
- Materiaal, bijzonderheid, groep, antwoord en formules zijn beschikbaar als Coding properties.
- Antwoordkeuzes zijn uit te lezen middels een ConceptMap.
#### __NHG-Tabel 48 - Bepalingenclusters__
- De juiste FHIR-representatie van deze tabel is nog niet duidelijk, en volgt op een later moment.
#### __NHG-Tabel 49 - Ingrepen en behandelingen__
- In overleg met NHG nu geen prioriteit, en derhalve niet beschikbaar.
#### __NHG-Tabel 50 - ICPC en attentiewaarde__
- Deze tabel is beschikbaar als een collectie ValueSets. De URI wordt bepaald volgens het format https://referentiemodel.nhg.org/tabellen/nhg-tabel-50-ICPC-en-attentiewaarde#categorie-{categorie}, waarbij {categorie} vervangen wordt door de betreffende categorie. De ValueSets bevatten de codes uit tabel 24 (ICPC-1-NL) uit de betreffende categorie.
- De ValueSets zijn te vinden middels een request op het ValueSet endpoint met parameter url:below="https://referentiemodel.nhg.org/tabellen/nhg-tabel-50-ICPC-en-attentiewaarde"
- Versie opname / mutatie / vervallen zijn niet beschikbaar, aangezien een ValueSet geen properties kan toevoegen aan een CodeSystem. Wanneer kolom Versie vervallen echter gevuld is, zal het concept niet in de ValueSet geplaatst worden.
#### __NHG-Tabel 51 - Koppeltabel familieanamnese en bepalingen__
- Versie opname / mutatie / vervallen zijn niet beschikbaar, aangezien een ConceptMap geen properties kan bevatten. Wanneer kolom Versie vervallen echter gevuld is, zal het concept niet in de ConceptMap geplaatst worden.
#### __NHG-Tabel 54 - Reden stoppen of wijzigen van voorschrift__
- VO / VM / VV zijn beschikbaar als properties, respectievelijk Versie opname / Versie mutatie / Versie vervallen.
- Kolom HIS-code wordt gebruikt als de concept-code.
#### __NHG-Tabel 55 - Ernst overgevoeligheidsreactie__
- VO / VM / VV zijn beschikbaar als properties, respectievelijk Versie opname / Versie mutatie / Versie vervallen.
#### __NHG-Tabel 56 - Profylaxe__
- VO / VM / VV zijn beschikbaar als properties, respectievelijk Versie opname / Versie mutatie / Versie vervallen.
#### __NHG-Tabel 57 - Episodebundels__
- CodeSystem met bundels, waarbij per code een property verwijst naar de URI van een ValueSet met bundels
- Deze tabel is beschikbaar als een CodeSystem waarin elk concept een bundel vertegenwoordigd. Elk concept heeft een property __ValueSet URI__ waarin de URI te vinden is van de ValueSet waarin de juiste episodecodes beschreven staan.
#### __NHG-Tabel 58 - ICPC en griepselectie__
- Deze tabel is nog niet beschikbaar. De waarschijnlijke verschijningsvorm is een CodeSystem supplement, echter deze is niet te maken zonder dat de tabel een verwijzing bevat naar de juiste ICPC-1-NL-versie. Zodra deze verwijzing toegevoegd is kan deze resource toegevoegd worden.
#### __NHG-Tabel 59 - Taken__
- VO / VM / VV zijn beschikbaar als properties, respectievelijk Versie opname / Versie mutatie / Versie vervallen.
#### __NHG-Tabel 60 - Preventieprogramma Griepvaccinatie__
- VO / VM / VV zijn beschikbaar als properties, respectievelijk Versie opname / Versie mutatie / Versie vervallen.
- Het stapnummer en de vervolgstap zijn beschikbaar als integer properties. Codes hebben geen display.
#### __NHG-Tabel 62 - Behandelgrenzen__
- VO / VM / VV zijn beschikbaar als properties, respectievelijk Versie opname / Versie mutatie / Versie vervallen.
#### __NHG-Tabel 63 - Categorie mogelijke vertegenwoordiging patiënt__
- VO / VM / VV zijn beschikbaar als properties, respectievelijk Versie opname / Versie mutatie / Versie vervallen.
- Kolom voor minderjarige is beschikbaar als een boolean property.
#### __NHG-Tabel 64 - Behandelingen en contra-indicatie aarden__
- Deze tabel is nog niet beschikbaar, aangezien deze voortbouwt op tabel 49, welke geen prioriteit heeft. De waarschijnlijke verschijningsvorm, indien relevant, is een ConceptMap.
#### __NHG-Tabel 65 - ICPC en patiëntvriendelijke titels__
- VO / VM / VV zijn beschikbaar als properties, respectievelijk Versie opname / Versie mutatie / Versie vervallen.
- Dit is een CodeSystem supplement. De resource is op dit moment niet te gebruiken als daadwerkelijk supplement, aangezien de release geen expliciete verwijzing naar een versie van de ICPC-1-NL heeft. In overleg met het NHG wordt deze verwijzing in de toekomst toegevoegd. Tot die tijd is het in de meeste FHIR-servers als losstaand CodeSystem te gebruiken.
#### __NHG-Tabel 66 - Infectieziekten waartegen gevaccineerd kan worden__
- Deze tabel is nog niet beschikbaar.
#### __NHG-Tabel 67 - Preventieprogramma Pneumokokkenvaccinatie__
- Het stapnummer en de vervolgstap zijn beschikbaar als integer properties. Codes hebben geen display.
#### __NHG-Tabel 68 - Bepalingen nierfunctie__
- Deze tabel is beschikbaar als ValueSet. De codes in de ValueSet zijn afkomstig uit tabel 45.
#### __NHG-Tabel 69 - Soort correspondentie__
- VO / VM / VV zijn beschikbaar als properties, respectievelijk Versie opname / Versie mutatie / Versie vervallen.
#### __NHG-Tabel 70 - Soort sociaal gegeven__
- VO / VM / VV zijn beschikbaar als properties, respectievelijk Versie opname / Versie mutatie / Versie vervallen.
#### __NHG-Tabel 71 - Primaire autorisatierollen__
- VO / VM / VV zijn beschikbaar als properties, respectievelijk Versie opname / Versie mutatie / Versie vervallen.

## Aandachtspunten bij het gebruik van NHG-tabellen via de Terminologieserver
- De URI's van NHG resources zijn vooralsnog niet vastgelegd in de FHIR Terminology Authority documentatie (https://confluence.hl7.org/display/TA/). Dit proces loopt nog.

## Beschikbare FHIR-resources
De gegenereerde resources hebben de volgende URIs:

|Tabel|Titel|Type|URI|
|:---|:---|:---|:---|
|NHG-Tabel 2|Soort functie|CodeSystem|https://referentiemodel.nhg.org/tabellen/nhg-tabel-2-soort-functie|
|NHG-Tabel 4|Geslacht|CodeSystem|https://referentiemodel.nhg.org/tabellen/nhg-tabel-4-geslacht|
|NHG-Tabel 5|Categorie patiënt|CodeSystem|https://referentiemodel.nhg.org/tabellen/nhg-tabel-5-categorie-patient|
|NHG-Tabel 6|Burgerlijke staat|CodeSystem|https://referentiemodel.nhg.org/tabellen/nhg-tabel-6-burgerlijke-staat|
|NHG-Tabel 9|Reden vertrek|CodeSystem|https://referentiemodel.nhg.org/tabellen/nhg-tabel-9-reden-vertrek|
|NHG-Tabel 12|Soort derde|CodeSystem|https://referentiemodel.nhg.org/tabellen/nhg-tabel-12-soort-derde|
|NHG-Tabel 14|Contactwijze|CodeSystem|https://referentiemodel.nhg.org/tabellen/nhg-tabel-14-contactwijze|
|NHG-Tabel 15|Verrichtingen|Niet beschikbaar|Niet beschikbaar|
|NHG-Tabel 20|Code opleiding|CodeSystem|https://referentiemodel.nhg.org/tabellen/nhg-tabel-20-code-opleiding|
|NHG-Tabel 24|ICPC|CodeSystem|http://hl7.org/fhir/sid/icpc-1-nl|
|NHG-Tabel 25|Gebruiksvoorschrift - tekstcategorieen|CodeSystem|https://referentiemodel.nhg.org/tabellen/nhg-tabel-25-gebruiksvoorschrift#tekstcategorieen|
|NHG-Tabel 25|Gebruiksvoorschrift - tijdseenheid|CodeSystem|https://referentiemodel.nhg.org/tabellen/nhg-tabel-25-gebruiksvoorschrift#tijdseenheid|
|NHG-Tabel 25|Gebruiksvoorschrift - aanvullende teksten|CodeSystem|https://referentiemodel.nhg.org/tabellen/nhg-tabel-25-gebruiksvoorschrift#aanvullende_teksten|
|NHG-Tabel 25|Gebruiksvoorschrift - gebruikseenheid|CodeSystem|https://referentiemodel.nhg.org/tabellen/nhg-tabel-25-gebruiksvoorschrift#gebruikseenheid|
|NHG-Tabel 27|ICPC en contra-indicatie aarden|ConceptMap|https://referentiemodel.nhg.org/tabellen/nhg-tabel-27-ICPC-en-contra-indicatie-aarden|
|NHG-Tabel 42|Aanduiding naamgebruik|CodeSystem|https://referentiemodel.nhg.org/tabellen/nhg-tabel-42-aanduiding-naamgebruiker|
|NHG-Tabel 43|Familierelaties|CodeSystem|https://referentiemodel.nhg.org/tabellen/nhg-tabel-43-familierelaties|
|NHG-Tabel 45|Diagnostische bepalingen|CodeSystem|https://referentiemodel.nhg.org/tabellen/nhg-tabel-45-diagnostische-bepalingen|
|NHG-Tabel 45|Diagnostische bepalingen - hulptabel groep|CodeSystem|https://referentiemodel.nhg.org/tabellen/nhg-tabel-45-diagnostische-bepalingen#groep|
|NHG-Tabel 45|Diagnostische bepalingen - hulptabel materialen|CodeSystem|https://referentiemodel.nhg.org/tabellen/nhg-tabel-45-diagnostische-bepalingen#materialen|
|NHG-Tabel 45|Diagnostische bepalingen - hulptabel formules|CodeSystem|https://referentiemodel.nhg.org/tabellen/nhg-tabel-45-diagnostische-bepalingen#formules|
|NHG-Tabel 45|Diagnostische bepalingen - hulptabel bijzonderheid|CodeSystem|https://referentiemodel.nhg.org/tabellen/nhg-tabel-45-diagnostische-bepalingen#bijzonderheid|
|NHG-Tabel 45|Diagnostische bepalingen - hulptabel antwoord|CodeSystem|https://referentiemodel.nhg.org/tabellen/nhg-tabel-45-diagnostische-bepalingen#antwoord|
|NHG-Tabel 45|Diagnostische bepalingen - hulptabel antwoordkeuze|ConceptMap|https://referentiemodel.nhg.org/tabellen/nhg-tabel-45-diagnostische-bepalingen#antwoordkeuze|
|NHG-Tabel 48|Bepalingenclusters|Niet beschikbaar|Niet beschikbaar|
|NHG-Tabel 49|Ingrepen en behandelingen|Niet beschikbaar|Niet beschikbaar|
|NHG-Tabel 50|ICPC en attentiewaarde|ValueSet|https://referentiemodel.nhg.org/tabellen/nhg-tabel-50-ICPC-en-attentiewaarde#categorie-1|
|NHG-Tabel 50|ICPC en attentiewaarde|ValueSet|https://referentiemodel.nhg.org/tabellen/nhg-tabel-50-ICPC-en-attentiewaarde#categorie-2|
|NHG-Tabel 51|Koppeltabel familieanamnese en bepalingen |ConceptMap|https://referentiemodel.nhg.org/tabellen/nhg-tabel-51-familieanamnese-en-diagnostische-bepalingen|
|NHG-Tabel 54|Reden stoppen of wijzigen van voorschrift |CodeSystem|https://referentiemodel.nhg.org/tabellen/nhg-tabel-54-reden-stoppen-of-wijzigen-voorschrift|
|NHG-Tabel 55|Ernst overgevoeligheidsreactie |CodeSystem|https://referentiemodel.nhg.org/tabellen/nhg-tabel-55-ernst-overgevoeligheidsreactie|
|NHG-Tabel 56|Profylaxe |CodeSystem|https://referentiemodel.nhg.org/tabellen/nhg-tabel-56-profylaxe|
|NHG-Tabel 57|Episodebundels|Niet beschikbaar|Niet beschikbaar|
|NHG-Tabel 58|ICPC en griepselectie|Niet beschikbaar|Niet beschikbaar|
|NHG-Tabel 59|Taken|CodeSystem|https://referentiemodel.nhg.org/tabellen/nhg-tabel-59-taken|
|NHG-Tabel 60|Preventieprogramma Griepvaccinatie|CodeSystem|https://referentiemodel.nhg.org/tabellen/nhg-tabel-60-preventieprogramma-griepvaccinatie|
|NHG-Tabel 62|Behandelgrenzen|CodeSystem|https://referentiemodel.nhg.org/tabellen/nhg-tabel-62-behandelgrenzen|
|NHG-Tabel 63|Categorie mogelijke vertegenwoordiging patiënt|CodeSystem|https://referentiemodel.nhg.org/tabellen/nhg-tabel-63-categorie-mogelijke-vertegenwoordiging-patiënt|
|NHG-Tabel 64|Behandelingen en contra-indicatie aarden|Niet beschikbaar|Niet beschikbaar|
|NHG-Tabel 65|ICPC en patiëntvriendelijke titels|CodeSystem|https://referentiemodel.nhg.org/tabellen/nhg-tabel-65-ICPC-en-patiëntvriendelijke-titel|
|NHG-Tabel 66|Infectieziekten waartegen gevaccineerd kan worden|CodeSystem|https://referentiemodel.nhg.org/tabellen/nhg-tabel-66-infectieziekten-waartegen-gevaccineerd-kan-worden|
|NHG-Tabel 67|Preventieprogramma Pneumokokkenvaccinatie|CodeSystem|https://referentiemodel.nhg.org/tabellen/nhg-tabel-67-preventieprogramma-pneumokkenvaccinatie|
|NHG-Tabel 68|Bepalingen nierfunctie|ValueSet|https://referentiemodel.nhg.org/tabellen/nhg-tabel-68-bepalingen-nierfunctie|
|NHG-Tabel 69|Soort correspondentie|CodeSystem|https://referentiemodel.nhg.org/tabellen/nhg-tabel-69-soort-correspondentie|
|NHG-Tabel 70|Soort sociaal gegeven|CodeSystem|https://referentiemodel.nhg.org/tabellen/nhg-tabel-70-soort-sociaal-gegeven|
|NHG-Tabel 71|Primaire autorisatierollen|CodeSystem|https://referentiemodel.nhg.org/tabellen/nhg-tabel-71-primaire-autorisatierollen|

## Schema updates
- Middels een geautomatiseerd proces wordt minimaal eenmaal per week de meest recente versie gedownload. Oudere versies komen daarmee te vervallen en zijn niet meer op te vragen.