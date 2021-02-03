# ART-DECOR-mirroring
## Auteurs
__Geschreven door__: Sander Mertens, projectleider Terminologieserver

__Review door__: 

## Inleiding
In dit document wordt de werkwijze omtrent het spiegelen van FHIR-resources uit de Nictiz-instantie van ART-DECOR (decor.nictiz.nl) gedocumenteerd. Het doel van dit document is inzichtelijk maken welke keuzes er gemaakt zijn voor dit proces, welke wijzigingen er ten opzichte van de versie op decor.nictiz.nl gemaakt worden, en wat de aandachtspunten bij het gebruik van deze resources zijn.

## Overzicht gespiegelde terminologie-resources
### Actueel
- Codestelsels zibs 2020 (https://decor.nictiz.nl/decor/services/CodeSystemIndex?prefix=zib2020bbr-)
- Waardelijsten zibs 2020 (https://decor.nictiz.nl/decor/services/ValueSetIndex?prefix=zib2020bbr-)

## Methode verzamelen van FHIR-resources
- Door middel van een script worden de links naar FHIR R4-resources verzameld van de onder _Actueel_ genoemde webpagina's.
- Alle links worden benaderd en de resources gedownload.
- Uit de resources worden een aantal gegevens verwijderd:
  - id: Het ID is een business identifier afkomstig uit ART-DECOR. Deze wordt verwijderd, waarna ontoservereen nieuwe lokale ID kan genereren.
- Er worden een aantal gegevens aan de resource toegevoegd:
  - Codestelsels:
    - Indien een codestelsel geen valueSet key bevat (een url voor een implicit valueset die het hele codesysteem omvat) wordt deze gegenereerd volgens het format: {{url}}?vs. Met andere woorden: er wordt *?vs* achter de URL geplaatst. Hiermee is het mogelijk om de server aan te roepen met https://terminologieserver.nl/fhir/ValueSet/$expand?url={{url}}?vs , met alle mogelijke parameters voor een $expand request.
- Na deze bewerkingen wordt de terminologieserver verzocht om een overzicht van resources met dezelfde url en versie als de resource afkomstig uit ART-DECOR. Wanneer er een resource bestaat met deze criteria, zal de resource geupdate worden. Wanneer deze niet bestaat, wordt deze als een nieuwe resource geupload.
- Direct volgend op het uploaden wordt een meta security label (https://www.hl7.org/fhir/security-labels.html) toegevoegd, waarmee toegang tot de resource beperkt kan worden tot een groep gebruikers. Voor resources afkomstig uit ART-DECOR is dit label 'artdecor.read'.

## Aandachtspunten bij het gebruik van resources afkomstig van decor.nictiz.nl
- Voor deze resources is de keuze gemaakt om bij iedere update de oude versie te overschrijven. Hierdoor wordt op de terminologieserver de resource.meta.lastUpdated bijgewerkt naar de datum/tijd van updaten, en de waarde van resource.meta.versionId met 1 verhoogd. Hiermee is het voor de gebruiker inzichtelijk wanneer de meest recente synchronisatie met ART-DECOR is uitgevoerd.
- Inhoudelijk zijn deze resources na uploaden gelijk aan de inhoud op decor.nictiz.nl. Wel zal logischerwijs de ID en metadata van de resource verschillen. Aangezien decor.nictiz.nl de bron (_source of truth_) van de resource is, kan het zijn dat een wijziging nog niet in de terminologieserver doorgevoerd is. Dit zal zeldzaam en maar zelden relevant zijn voor de gebruiker, maar dient wel benoemd te worden.

## Schema updates
- Bij grote wijzigingen van de gegevens op ART-DECOR worden de resources gesynchroniseerd. Er wordt gewerkt aan een methode waarbij de resources ofwel bij wijziging, ofwel op een vast tijdstip worden bijgewerkt. Zodra hier een besluit over genomen is zal deze documentatie worden bijgewerkt.