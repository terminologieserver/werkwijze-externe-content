# HL7
## Auteurs
__Geschreven door__: Sander Mertens, projectleider Terminologieserver

__Review door__: 

## Inleiding
In dit document wordt de werkwijze omtrent het spiegelen van FHIR-resources van terminology.HL7.org gedocumenteerd. Het doel van dit document is inzichtelijk maken welke keuzes er gemaakt zijn voor dit proces, welke wijzigingen er ten opzichte van de bron gemaakt worden, en wat de aandachtspunten bij het gebruik van deze resources zijn.

## Overzicht gespiegelde terminologie-resources
### Actueel (per 1 februari 2021)
  - CodeSystem
    - https://terminology.hl7.org/2.0.0/CodeSystem-v3-NullFlavor.json
    - https://terminology.hl7.org/2.0.0/CodeSystem-v3-ActCode.json
    - https://terminology.hl7.org/2.0.0/CodeSystem-v3-AdministrativeGender.json
  - ValueSet
    - https://terminology.hl7.org/2.0.0/ValueSet-v3-NullFlavor.json
    - https://terminology.hl7.org/2.0.0/ValueSet-v3-ObservationDiagnosisTypes.json
    - https://www.hl7.org/fhir/valueset-observation-vitalsignresult.json
    - https://www.hl7.org/fhir/valueset-ucum-units.json

## Methode verzamelen van FHIR-resources
- Door middel van een script worden de links naar FHIR R4-resources verzameld van de onder _Actueel_ genoemde webpagina's.
- Alle links worden benaderd en de resources gedownload.
- Uit de resources worden een aantal gegevens verwijderd:
  - id: Het ID is een business identifier afkomstig uit de bron. Deze wordt verwijderd, waarna ontoserver een lokale ID kan genereren.
- Er worden een aantal gegevens aan de resource toegevoegd:
  - Codestelsels:
    - Indien een codestelsel geen valueSet key bevat (een url voor een implicit valueset die het hele codesysteem omvat) wordt deze gegenereerd volgens het format: {{url}}?vs. Met andere woorden: er wordt *?vs* achter de URL geplaatst. Hiermee is het mogelijk om de server aan te roepen met https://terminologieserver.nl/fhir/ValueSet/$expand?url={{url}}?vs , met alle mogelijke parameters voor een $expand request.
- Na deze bewerkingen wordt de terminologieserver verzocht om een overzicht van resources met dezelfde url en versie als de resource afkomstig uit de bron. Wanneer er een resource bestaat met deze criteria, zal de resource geupdate worden. Wanneer deze niet bestaat, wordt deze als een nieuwe resource geupload.

## Aandachtspunten bij het gebruik van resources afkomstig van terminology.hl7.org
- Voor deze resources is de keuze gemaakt om bij iedere update de oude versie te overschrijven. Hierdoor wordt op de terminologieserver de resource.meta.lastUpdated bijgewerkt naar de datum/tijd van updaten, en de waarde van resource.meta.versionId met 1 verhoogd. Hiermee is het voor de gebruiker inzichtelijk wanneer de meest recente synchronisatie met HL7 is uitgevoerd.
- Inhoudelijk zijn deze resources na uploaden gelijk aan de inhoud op terminology.hl7.org. Wel zal logischerwijs de ID en metadata van de resource verschillen. Aangezien hl7 de bron (_source of truth_) van de resource is, kan het zijn dat een wijziging nog niet in de terminologieserver doorgevoerd is. Dit zal maar zelden relevant zijn voor de gebruiker, maar dient wel benoemd te worden.

## Schema updates
- Er wordt gewerkt aan een methode waarbij de resources ofwel bij wijziging, ofwel op een vast tijdstip worden bijgewerkt. Zodra hier een besluit over genomen is zal deze documentatie worden bijgewerkt.