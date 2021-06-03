# HGNC
## Auteurs
__Geschreven door__: Sander Mertens

__Review door__: 

## Inleiding
In dit document wordt de werkwijze omtrent het uploaden van de __HUGO Gene Nomenclature Committee__ gedocumenteerd. Het doel van dit document is inzichtelijk maken welke keuzes er gemaakt zijn voor dit proces, welke wijzigingen er ten opzichte van de bron gemaakt worden, en wat de aandachtspunten bij het gebruik van deze resources zijn.

## Methode
- De release files van de HGNC worden naar FHIR-formaat getransformeerd middels een open-source script geleverd door CSIRO. Dit script is te vinden op https://github.com/aehrc/fhir-hgnc. Hieruit volgen twee resources: het _gene groups_ en _gene ids_ codesystem. Deze resources worden geupload naar de NTS.
- Bij het uploaden wordt een meta security label (https://www.hl7.org/fhir/security-labels.html) toegevoegd, waarmee toegang tot de resource beperkt kan worden tot een groep gebruikers. Dit label is voor HGNC 'hgnc.read'.
-Aan de codesystems wordt een valueSet key toegevoegd. Dit is een url voor een implicit valueset die het hele codesysteem omvat. Deze wordt gegenereerd volgens het format: {{url}}?vs. Met andere woorden: er wordt *?vs* achter de URL geplaatst. Hiermee is het mogelijk om de server aan te roepen met https://terminologieserver.nl/fhir/ValueSet/$expand?url={{url}}?vs , met alle mogelijke parameters voor een $expand request.

## Beschikbare FHIR-resources
De gegenereerde CodeSystems hebben de volgende URIs:

|CodeSystem|URI|
|:---|:---|
|Gene groups|http://www.genenames.org/genegroup|
|Gene IDs|http://www.genenames.org/geneId|

## Schema updates
- Middels een geautomatiseerd proces wordt minimaal eenmaal per week de meest recente versie van de HGNC gedownload van https://www.genenames.org/download/statistics-and-files/. Vervolgens wordt de transformatie uitgevoerd zoals beschreven onder _methode_.
