# LOINC
## Auteurs
__Geschreven door__: Sander Mertens, projectleider Terminologieserver

__Review door__: 

## Inleiding
In dit document wordt de werkwijze omtrent het uploaden van de LOINC-standaard gedocumenteerd. Het doel van dit document is inzichtelijk maken welke keuzes er gemaakt zijn voor dit proces, welke wijzigingen er ten opzichte van de bron gemaakt worden, en wat de aandachtspunten bij het gebruik van deze resources zijn.

## Methode
- Bij het verschijnen van een nieuwe versie van LOINC wordt deze geïndexeerd middels een applicatie die ontwikkeld is voor Ontoserver door CSIRO.
- Bij het uploaden wordt een meta security label (https://www.hl7.org/fhir/security-labels.html) toegevoegd, waarmee toegang tot de resource beperkt kan worden tot een groep gebruikers. Voor resources afkomstig uit LOINC is dit label 'loinc.read'.

## Aandachtspunten bij het gebruik van LOINC via de Terminologieserver
- Voor LOINC worden nieuwe versies als een nieuwe business version geüpload. Hierdoor blijft de vorige versie ook toegankelijk. Het streven is om naast de nieuwste versie __1__ vorige versie aan te bieden.

## Schema updates
- Bij het verschijnen van een nieuwe versie van LOINC zal de nieuwe versie zo spoedig mogelijk verwerkt worden in de terminologieserver.