# ICD-10-NL
## Auteurs
__Geschreven door__: Sander Mertens

__Review door__: 

## Inleiding
In dit document wordt de werkwijze omtrent het uploaden van de __ICD-10-NL__ gedocumenteerd. Het doel van dit document is inzichtelijk maken welke keuzes er gemaakt zijn voor dit proces, welke wijzigingen er ten opzichte van de bron gemaakt worden, en wat de aandachtspunten bij het gebruik van deze resources zijn.

## Methode
- De release files van de HPO worden naar FHIR-formaat getransformeerd middels het script beschikbaar op https://github.com/aehrc/fhir-claml. Hieruit volgt 1 resource: het _icd-10-nl_ codesystem.
- Het fhir-claml transformatiescript kan niet omgaan met het formaat van de includeDescendants en description informatie uit het ClaML-bestand. Deze zijn niet op een standaard wijze geformatteerd en bevatten ook geen informatie die bruikbaar is in het CodeSystem - derhalve worden deze op geautomatiseerde wijze verwijderd.
- Bij het uploaden wordt een meta security label (https://www.hl7.org/fhir/security-labels.html) toegevoegd, waarmee toegang tot de resource beperkt kan worden tot een groep gebruikers. Dit label is voor HPO 'icd10.read'.
- Aan de codesystems wordt een valueSet key toegevoegd. Dit is een url voor een implicit valueset die het hele codesysteem omvat. Deze wordt gegenereerd volgens het format: {{url}}?vs. Met andere woorden: er wordt *?vs* achter de URL geplaatst. Hiermee is het mogelijk om de server aan te roepen met https://terminologieserver.nl/fhir/ValueSet/$expand?url={{url}}?vs , met alle mogelijke parameters voor een $expand request.
- De versionering van de ICD-10 is geen timestamp. De Nederlandse versie heeft de versie-aanduiding "ICD-10 2021" meegekregen in het ClaML bestand. Deze versie-aanduiding is overgenomen in de FHIR-representatie.

## Aandachtspunten bij het gebruik van ICD-10-NL via de Terminologieserver
- De eigenschappen _kind_, _referer_, _relatedTerm_, _remark_, _inclusion_, _exclusion_, _asteriskHead_, _asteriskCategory_, _coding-hint_, _footnote_ en _note_ worden als gelijknamige properties beschikbaar gesteld in het CodeSystem.

## Beschikbare FHIR-resources
De gegenereerde CodeSystems hebben de volgende URIs:

|Type|Titel|URI|
|:---|:---|:---|
|CodeSystem|ICD-10 2021 v1b|http://hl7.org/fhir/sid/icd-10-nl|

## Schema updates
- Updates worden handmatig uitgevoerd bij het verschijnen van een nieuwe versie van de ICD-10-NL.
