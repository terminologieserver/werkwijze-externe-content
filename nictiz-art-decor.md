# ART-DECOR-mirroring
## Auteurs
__Geschreven door__: Sander Mertens

__Review door__: Michael Verbeek

## Inleiding
In dit document wordt de werkwijze omtrent het spiegelen van FHIR-resources uit de Nictiz-instantie van ART-DECOR (decor.nictiz.nl) gedocumenteerd. Het doel van dit document is inzichtelijk maken welke keuzes er gemaakt zijn voor dit proces, welke wijzigingen er ten opzichte van de versie op decor.nictiz.nl gemaakt worden, en wat de aandachtspunten bij het gebruik van deze resources zijn.

## Overzicht gespiegelde terminologie-resources
### Actueel
- Codestelsels zibs 2020 (https://decor.nictiz.nl/decor/services/CodeSystemIndex?prefix=zib2020bbr-)
- Waardelijsten zibs 2020 (https://decor.nictiz.nl/decor/services/ValueSetIndex?prefix=zib2020bbr-)
- Codestelsels zibs 2017 (https://decor.nictiz.nl/decor/services/CodeSystemIndex?prefix=zib2020bbr-)
- Waardelijsten zibs 2017 (https://decor.nictiz.nl/decor/services/ValueSetIndex?prefix=zib2020bbr-)
- Codestelsels (http://decor.nictiz.nl/decor/services/CodeSystemIndex?prefix=nictiz2bbr-)
- Waardelijsten (http://decor.nictiz.nl/decor/services/ValueSetIndex?prefix=nictiz2bbr-)

## Methode verzamelen van FHIR-resources
- Door middel van een script worden de links naar FHIR R4-resources verzameld van de onder _Actueel_ genoemde webpagina's.
- Alle links van resources met status _Definitief_ worden benaderd en gedownload.
- Uit de resources worden een aantal gegevens verwijderd:
  - id: Het ID is een business identifier afkomstig uit ART-DECOR. Deze wordt verwijderd, waarna ontoserver een nieuwe lokale ID kan genereren.
- Er worden een aantal gegevens aan de resource toegevoegd:
  - Codestelsels:
    - Indien een codestelsel geen valueSet key bevat (een url voor een implicit valueset die het hele codesysteem omvat) wordt deze gegenereerd volgens het format: {{url}}?vs. Met andere woorden: er wordt *?vs* achter de URL geplaatst. Hiermee is het mogelijk om de server aan te roepen met https://terminologieserver.nl/fhir/ValueSet/$expand?url={{url}}?vs , met alle mogelijke parameters voor een $expand request.
- Na deze bewerkingen wordt de terminologieserver verzocht om een overzicht van resources met dezelfde url en versie als de resource afkomstig uit de benaderde ART-DECOR index. Wanneer er een resource bestaat met deze criteria, zal de resource geupdate worden. Wanneer deze niet bestaat, wordt deze als een nieuwe resource geupload.
- Direct volgend op het uploaden wordt een meta security label (https://www.hl7.org/fhir/security-labels.html) toegevoegd, waarmee toegang tot de resource beperkt kan worden tot een groep gebruikers. Voor resources afkomstig van bovengenoemde indices uit ART-DECOR is dit label 'artdecor.read'.

## Schema updates
- Bij grote wijzigingen van de gegevens op ART-DECOR worden de resources handmatig gesynchroniseerd. Hiernaast wordt meermaals per week geautomatiseerd gesynchroniseerd.

## Aandachtspunten bij het gebruik van resources afkomstig van decor.nictiz.nl
### Updates en inhoud
- Voor deze resources is de keuze gemaakt om bij iedere update de oude versie te overschrijven, zolang het versienummer overeen komt. Hierdoor wordt op de terminologieserver de resource.meta.lastUpdated bijgewerkt naar de datum/tijd van updaten, en de waarde van resource.meta.versionId met 1 verhoogd. Hiermee is het voor de gebruiker inzichtelijk wanneer de meest recente synchronisatie met ART-DECOR is uitgevoerd. Het updaten van metadata zoals de security tag levert een fractionele verhoging van de resource.meta.versionId waarde op.
- Inhoudelijk zijn deze resources na uploaden gelijk aan de inhoud op decor.nictiz.nl. Wel zal logischerwijs de ID en metadata van de resource verschillen. Aangezien decor.nictiz.nl de bron (_source of truth_) van de resource is, kan het zijn dat een wijziging nog niet in de terminologieserver doorgevoerd is. Dit zal zeldzaam en maar zelden relevant zijn voor de gebruiker, maar dient wel benoemd te worden.
- CodeSystems en ValueSets die in meerdere versies van de zibs voorkomen, zijn meerdere keren aanwezig op de server. Het onderscheid is te maken door de _version_: deze komt overeen met de datetime op het moment van de release van de betreffende zib. Voor de zibs 2017 is dat __2017-12-31T00:00:00__, en voor 2020 is dat __2020-09-01T00:00:00__. Deze versie-aanduiding kan wijzigen - voor vragen hieromtrent is het zibcentrum het juiste aanspreekpunt.
### $expand / $lookup operation
- Bijzondere aandacht is vereist bij het uitvoeren van een $expand of $lookup operation. Waardelijsten zoals GCS_MotorCodelijsten (https://decor.nictiz.nl/art-decor/decor-valuesets--zib2020bbr-?id=2.16.840.1.113883.2.4.3.11.60.121.11.5) bevatten voor elk concept een ordinale numerieke waarde. Deze ordinale waarde is zichtbaar in zowel de ValueSet als het CodeSystem, maar standaard niet in de $expand of $lookup operation. De $expand operation geeft standaard alleen het CodeSystem, version, code en display retour per de FHIR R4 specificatie. Zie bijvoorbeeld dit concept uit de expansion.contains lijst:

      [...]{
        "system": "urn:oid:2.16.840.1.113883.2.4.3.11.60.40.4.2.2",
        "version": "2017-12-31T00:00:00",
        "code": "M3",
        "display": "Buigreactie"
      },[...]

Omdat dit voor gebruikers mogelijk problemen kan opleveren (de ordinale waarde is immers noodzakelijk voor berekeningen), hebben we aan de ontwikkelaar van de NTS gevraagd om een pre-adoptie van het ValueSet.expansion.contains.property element uit R5 (https://build.fhir.org/valueset-definitions.html). Door aan de request de parameter __property=*__ mee te geven, worden alle properties in het antwoord meegestuurd. Dit werkt vergelijkbaar met de __property__ parameter in een $lookup: het is dus ook mogelijk om bijvoorbeeld alleen de parent property van de concepten in de expansion op te vragen door de parameter __property=parent__ te gebruiken.

Door __property=*__ mee te sturen met de $expand operation op de ValueSet voor GCS_MotorCodelijsten zal het antwoord alle properties omvatten expansion.contains:

    [...]{
      "extension": [
        {
          "url": "http://hl7.org/fhir/5.0/StructureDefinition/extension-ValueSet.expansion.contains.property",
            "extension": [
              {
                "url": "code",
                "valueCode": "inactive"
              },
              {
                "url": "value_x_",
                "valueBoolean": false
              }
            ]
        },
        {
          "url": "http://hl7.org/fhir/5.0/StructureDefinition/extension-ValueSet.expansion.contains.property",
          "extension": [
            {
              "url": "code",
              "valueCode": "ordinal-value"
            },
            {
              "url": "value_x_",
              "valueString": "3"
            }
          ]
        },
        {
          "url": "http://hl7.org/fhir/5.0/StructureDefinition/extension-ValueSet.expansion.contains.property",
          "extension": [
            {
              "url": "code",
              "valueCode": "definition"
            },
            {
              "url": "value_x_",
              "valueString": "Buigreactie [Armen en handen op krampachtige manier geplooid. Meestal benen gestrekt.]"
            }
          ]
        },
        {
          "url": "http://hl7.org/fhir/5.0/StructureDefinition/extension-ValueSet.expansion.contains.property",
          "extension": [
            {
              "url": "code",
              "valueCode": "status"
            },
            {
              "url": "value_x_",
              "valueString": "active"
            }
          ]
        }
      ],
      "system": "urn:oid:2.16.840.1.113883.2.4.3.11.60.40.4.2.2",
      "version": "2017-12-31T00:00:00",
      "code": "M3",
      "display": "Buigreactie"
    },[...]
