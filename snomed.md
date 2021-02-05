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
- __Versies__ Voor SNOMED worden nieuwe versies als een nieuwe business version geüpload. Hierdoor blijft de vorige versie ook toegankelijk. Het streven is om naast de nieuwste versie __1__ vorige versie aan te bieden. Wanneer een query uitgevoerd wordt op url=http://snomed.info/sct, wordt automatisch de nieuwste versie gebruikt.
- __Taalreferentiesets__ FHIR R4 bevat op het moment van schrijven geen efficiënte mogelijkheid om binnen designations termen te filteren op taalreferentieset. Om het toch mogelijk te maken om patiëntvriendelijke termen te benaderen, bieden we twee opties. Voor meer informatie rondom het gebruik van patiëntvriendelijke termen, zie het kopje __taalreferentiesets__ op deze pagina.
- Meer voorbeelden zijn te vinden in de postman collectie elders in deze repository (https://github.com/terminologieserver/postman_voorbeelden).

## Schema updates
- Bij het verschijnen van een nieuwe Nederlandse editie van SNOMED zal de nieuwe versie zo spoedig mogelijk verwerkt worden in de terminologieserver. 
- Gezien de beperkte usecase voor het gebruik van de Internationale Editie zal deze niet separaat verwerkt worden.

## Taalreferentiesets
Voor het gebruik van taalreferentiesets bieden we twee opties.
### Optie 1

Bij het aanroepen van de Nederlandse SNOMED-editie (dit is de standaard module: 11000146104) in de terminologieserver wordt de voorkeur gegeven aan displaytermen uit de Nederlandse Taalreferentieset (31000146106). Om het mogelijk te maken om een display (=Preferred Term) te tonen uit de Patiëntvriendelijke extensie, indexeren we SNOMED een tweede keer, waarbij we de voorkeur aan termen uit de Patiëntvriendelijke taalreferentieset (15551000146102) uit de Patiënt Friendly Terms extension (module 15561000146104).

Door een request uit te voeren met op module _15561000146104_ zal waar mogelijk een display value uit de patiëntvriendelijke taalreferentieset getoond worden. Indien deze niet bestaat zal teruggevallen worden op de Nederlandse taalreferentieset, en vervolgens op de Engelse taalreferentiesets.
- Voorbeeldrequest patiëntvriendelijke termen:
  - https://r4.ontoserver.csiro.au/fhir/CodeSystem/$lookup?code=74400008&system=http://snomed.info/sct&version=http://snomed.info/sct/15561000146104
  - https://r4.ontoserver.csiro.au/fhir/ValueSet/$expand?url=http://snomed.info/sct/15561000146104?fhir_vs=ecl/233604007
- Voorbeeldrequest standaard Nederlands:
  - https://r4.ontoserver.csiro.au/fhir/CodeSystem/$lookup?code=74400008&system=http://snomed.info/sct&version=http://snomed.info/sct/11000146104
  - https://r4.ontoserver.csiro.au/fhir/ValueSet/$expand?url=http://snomed.info/sct/11000146104?fhir_vs=ecl/233604007 
  - _NB: Het weglaten van 11000146104 leidt hierbij tot hetzelfde resultaat, aangezien wij dit als standaardmodule hebben ingesteld._

### Optie 2
De methode die onder optie 1 beschreven staat biedt enkel de mogelijkheid om de Preferred Term als _display_ term te tonen. Wanneer er meer synoniemen zijn, of een tekstdefinitie, is dit niet voldoende. Zoals eerder beschreven biedt FHIR geen mogelijkheden om aan te geven dat een synoniem een patiëntvriendelijke term is. Om deze functionaliteit toch te bieden, hebben wij een CodeSystem Supplement en bijbehorende explicit ValueSet gemaakt waarmee dit toch mogelijk is.

Dit supplement voegt twee zaken toe aan de bijbehorende Editie van SNOMED.
- __Display__ : De display in het CodeSystem wordt vervangen door de Preferred Term uit de Patiëntvriendelijke Extensie. Bij een $lookup worden de synoniemen en tekstdefinities uit de Patiëntvriendelijke extensie getoond als designations. 

  Bij een $expand op de bijbehorende ValueSet zal deze display aanwezig zijn als designation use.code=display en _language_ __nl-NL-x-patfr__.
- __Designation__ : Patiëntvriendelijke termen worden toegevoegd als designations met _language_ "nl-NL-x-patfr". NB: Bij een $expand op de ValueSet zullen ook designations uit de SNOMED Editie worden getoond. Op basis van de _language_ __nl-NL-x-patfr__ zijn de termen te herkennen.

  Voor concepten waarvoor een textdefinitie beschikbaar is, wordt deze als designation aan het concept toegevoegd. De property is een _string_, overeenkomend met de textdefinitie uit de SNOMED-editie. Deze _string_ kan een lange tekst bevatten, met eventueel markup of HTML. Meer informatie over dit type beschrijvingen kunt u vinden in de Release Notes van de SNOMED Editie op https://mlds.ihtsdotools.org. Een tekstdefinitie is te herkennen aan bijvoorbeeld de valueCoding.display="tekstdefinitie" en valueCoding.code="900000000000550004".

#### URI's
- CodeSystem:   https://terminologieserver.nl/uri/cs/snomed-patient-friendly-terms
- ValueSet:     https://terminologieserver.nl/uri/vs/snomed-patient-friendly-terms

#### Voorbeelden
__Voorbeeld 1__ $lookup
>https://terminologieserver.nl//fhir/CodeSystem/$lookup?system=https://terminologieserver.nl/uri/cs/snomed-patient-friendly-terms&code=74400008
```
{
    "resourceType": "Parameters",
    "parameter": [
        {
            "name": "name",
            "valueString": "patient-friendly-terms-snomed-nl"
        },
        {
            "name": "version",
            "valueString": "20200930"
        },
        {
            "name": "designation",
            "part": [
                {
                    "name": "language",
                    "valueCode": "nl-NL-x-patfr"
                },
                {
                    "name": "use",
                    "valueCoding": {
                        "system": "http://snomed.info/sct",
                        "code": "900000000000550004",
                        "display": "tekstdefinitie"
                    }
                },
                {
                    "name": "value",
                    "valueString": "Dit is een ontsteking van het wormvormig aanhangsel (appendix) van de blindedarm, die erge buikpijn veroorzaakt."
                }
            ]
        }
    ]
}
```
In deze $lookup is te zien dat de __display__ gelijk is aan de Preferred Term uit de Patiëntvriendelijke taalrefentieset.

__Voorbeeld 2__ $expand
> request

```
{
    "resourceType": "ValueSet",
    [.....],
    "expansion": {
        [....],
        "parameter": [
            {
                "name": "version",
                "valueUri": "https://terminologieserver.nl/uri/cs/snomed-patient-friendly-terms|20200930"
            },
            {
                "name": "version",
                "valueUri": "http://snomed.info/sct|http://snomed.info/sct/11000146104/version/20200930"
            },
            {
                "name": "displayLanguage",
                "valueCode": "nl-NL-x-patfr"
            },
            {
                "name": "count",
                "valueInteger": 2147483647
            },
            {
                "name": "offset",
                "valueInteger": 0
            },
            {
                "name": "filter",
                "valueString": "74400008"
            }
        ],
        "contains": [
            {
                "system": "http://snomed.info/sct",
                "version": "http://snomed.info/sct/11000146104/version/20200930",
                "code": "74400008",
                "display": "appendicitis",
                "designation": [
                    {
                        "language": "en",
                        "use": {
                            "system": "http://snomed.info/sct",
                            "code": "900000000000003001",
                            "display": "Fully specified name"
                        },
                        "value": "Appendicitis (disorder)"
                    },
                    {
                        "language": "en",
                        "use": {
                            "system": "http://snomed.info/sct",
                            "code": "900000000000013009",
                            "display": "Synonym"
                        },
                        "value": "Appendicitis"
                    },
                    {
                        "language": "nl",
                        "use": {
                            "system": "http://snomed.info/sct",
                            "code": "900000000000013009",
                            "display": "Synonym"
                        },
                        "value": "appendicitis"
                    },
                    {
                        "language": "nl",
                        "use": {
                            "system": "http://snomed.info/sct",
                            "code": "900000000000003001",
                            "display": "Fully specified name"
                        },
                        "value": "appendicitis (aandoening)"
                    },
                    {
                        "language": "nl",
                        "use": {
                            "system": "http://snomed.info/sct",
                            "code": "900000000000013009",
                            "display": "Synonym"
                        },
                        "value": "blindedarmontsteking"
                    },
                    {
                        "language": "nl-NL-x-patfr",
                        "use": {
                            "system": "http://snomed.info/sct",
                            "code": "900000000000550004",
                            "display": "tekstdefinitie"
                        },
                        "value": "Dit is een ontsteking van het wormvormig aanhangsel (appendix) van de blindedarm, die erge buikpijn veroorzaakt."
                    },
                    {
                        "language": "nl-NL-x-patfr",
                        "use": {
                            "system": "http://terminology.hl7.org/CodeSystem/designation-usage",
                            "code": "display"
                        },
                        "value": "blindedarmontsteking"
                    }
                ]
            }
        ]
    }
}
```
In deze $expand is te zien dat naast de display uit het codestelsel http://snomed.info/sct, er twee designations aanwezig zijn met language=__nl-NL-x-patfr__. De tekstdefinitie, en de preferred term uit het CodeSystem Supplement met patiëntvriendelijke termen.