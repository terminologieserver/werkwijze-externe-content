# SNOMED
## Auteurs
__Geschreven door__: Sander Mertens, projectleider Terminologieserver

__Review door__: 

## Inleiding
In dit document wordt de werkwijze omtrent het uploaden van de SNOMED-standaard gedocumenteerd. Het doel van dit document is inzichtelijk maken welke keuzes er gemaakt zijn voor dit proces, welke wijzigingen er ten opzichte van de bron gemaakt worden, en wat de aandachtspunten bij het gebruik van deze resources zijn.

## Methode
- Bij het verschijnen van een nieuwe versie van SNOMED wordt deze geïndexeerd middels een applicatie die ontwikkeld is voor Ontoserver door CSIRO.
- Bij het uploaden wordt een meta security label (https://www.hl7.org/fhir/security-labels.html) toegevoegd, waarmee toegang tot de resource beperkt kan worden tot een groep gebruikers. Voor resources afkomstig uit SNOMED is dit label 'snomed.read'.

## Aandachtspunten bij het gebruik van SNOMED via de Terminologieserver
- Voor SNOMED worden nieuwe versies als een nieuwe business version geüpload. Hierdoor blijft de vorige versie ook toegankelijk. Het streven is om naast de nieuwste versie __1__ vorige versie aan te bieden.
- Taalreferentiesets
  - Bij het aanroepen van de Nederlandse SNOMED-editie (dit is de standaard: module 11000146104) in de terminologieserver wordt de voorkeur gegeven aan termen uit de Nederlandse Taalreferentieset (31000146106). FHIR R4 bevat op het moment van schrijven geen mogelijkheid om termen (_designations_) te filteren op taalreferentieset. Om het toch mogelijk te maken om patiëntvriendelijke termen te benaderen, indexeren we SNOMED een tweede keer vanuit de Patiënt Friendly Terms extension (module 15561000146104). Bij deze bewerking geven we de voorkeur aan termen uit de Patiëntvriendelijke taalreferentieset (15551000146102).
  - Door een request uit te voeren met op module _15561000146104_ zal waar mogelijk een display value uit de patiëntvriendelijke taalreferentieset getoond worden. Indien deze niet bestaat zal teruggevallen worden op de Nederlandse taalreferentieset, en vervolgens op de Engelse taalreferentiesets.
  - Voorbeeldrequest patiëntvriendelijke termen:
    - https://r4.ontoserver.csiro.au/fhir/CodeSystem/$lookup?code=74400008&system=http://snomed.info/sct&version=http://snomed.info/sct/15561000146104
    - https://r4.ontoserver.csiro.au/fhir/ValueSet/$expand?url=http://snomed.info/sct/15561000146104?fhir_vs=ecl/233604007
  - Voorbeeldrequest standaard Nederlands:
    - https://r4.ontoserver.csiro.au/fhir/CodeSystem/$lookup?code=74400008&system=http://snomed.info/sct&version=http://snomed.info/sct/11000146104
    - https://r4.ontoserver.csiro.au/fhir/ValueSet/$expand?url=http://snomed.info/sct/11000146104?fhir_vs=ecl/233604007 
    - Het weglaten van 11000146104 leidt hierbij tot hetzelfde resultaat, aangezien wij dit als standaardmodule hebben ingesteld.
  - Meer voorbeelden zijn te vinden in de postman collectie elders in deze repository (https://github.com/terminologieserver/postman_voorbeelden).

## Schema updates
- Bij het verschijnen van een nieuwe Nederlandse editie van SNOMED zal de nieuwe versie zo spoedig mogelijk verwerkt worden in de terminologieserver. 
- Gezien de beperkte usecase voor het gebruik van de Internationale Editie zal deze niet separaat verwerkt worden.