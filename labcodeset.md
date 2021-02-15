# SNOMED
## Auteurs
__Geschreven door__: Feikje Hielkema-Raadsveld, functioneel beheerder Nederlandse Labcodeset

__Review door__: Sander Mertens, projectleider Terminologieserver

## Inleiding
In dit document wordt de werkwijze omtrent het uploaden van de Nederlandse Labcodeset gedocumenteerd. Het doel van dit document is inzichtelijk maken welke keuzes er gemaakt zijn voor dit proces, welke wijzigingen er ten opzichte van de bron gemaakt worden, en wat de aandachtspunten bij het gebruik van deze resources zijn.

## Methode
- Bij het verschijnen van een nieuwe versie van de Nederlandse Labcodeset wordt deze geïndexeerd middels een applicatie die ontwikkeld is voor Ontoserver door CSIRO.
- Bij het uploaden wordt een meta security label (https://www.hl7.org/fhir/security-labels.html) toegevoegd, waarmee toegang tot de resource beperkt kan worden tot een groep gebruikers. Dit label is voor de Labcodeset 'labcodeset.read'.

## Aandachtspunten bij het gebruik van de Nederlandse Labcodeset via de Terminologieserver
- De XML-versie van de Nederlandse Labcodeset kan codes uit de LOINC prerelease bevatten: concepten die al wel aangemaakt, maar nog niet gepubliceerd zijn door LOINC. Op dit moment worden prerelease-codesnog niet ondersteund door de Terminologieserver. Deze codes ontbreken dus op de Terminologieserver.
- De Terminologieserver biedt nog geen ondersteuning voor LOINC panels. De panelconcepten zijn wel vindbaar in de Terminologieserver, maar de informatie welke concepten lid van het panel zijn is nog niet beschikbaar.
- __Versies__ Nieuwe versies worden als een nieuwe business version geüpload. Hierdoor blijft de vorige versie ook toegankelijk. Het streven is om naast de nieuwste versie __1__ vorige versie aan te bieden.

## Schema updates
- Bij het verschijnen van een nieuwe editie van de Nederlandse Labcodeset zal de nieuwe versie zo spoedig mogelijk verwerkt worden in de Terminologieserver. 
- Bij het verschijnen van een nieuwe editie van LOINC zal de nieuwe versie ook zo spoedig mogelijk verwerkt worden in de Terminologieserver. Dit kan gevolgen hebben voor de Nederlandse vertalingen van de LOINC-concepten. Ook kunnen prerelease-concepten beschikbaar komen, omdat ze inmiddels gepubliceerd zijn.

## Ontwerp
De Nederlandse Labcodeset is niet een op zichzelf staand codestelsel, maar een combinatie van verschillende internationale terminologiestelsels: LOINC, SNOMED en UCUM. De wetenschappelijke verenigingen NVKC en NVMM hebben een subset van LOINC gekozen, voorzien van verbanden met SNOMED (materialen, uitkomsten) en UCUM (eenheid) en Nederlandse informatie (vertalingen, lokale panels) toegevoegd. Zowel de internationale als de Nederlandse versies van die standaarden beschouwen we als aparte FHIR resources en supplementen. De Labcodeset heeft vorm gekregen als een aantal Value Sets hierop. Om de Labcodeset beschikbaar te maken met de FHIR Terminologieserver, heeft Nictiz een transformatiescript laten ontwikkelen. Dit script is open source, en in te zien via https://github.com/aehrc/labcodeset-fhir-transform.

## Lacunes in beschikbare resources
Doordat LOINC nog niet compleet in FHIR beschikbaar is, zijn er een aantal vraagstukken die we voor nu nog niet kunnen beantwoorden. De huidige transformatie voorziet nog niet in de volgende punten:
### Panels
Regenstrief heeft nog geen definitief formaat gekozen voor het uitdrukken van panels. Wij zijn hard bezig consensus te bereiken met Regenstrief, waarna deze in een gelijkend formaat worden toegevoegd in onze transformatie van de labcodeset.

### Pre-release LOINC codes
Regenstrief maakt goedgekeurde maar nog ongepubliceerde codes beschikbaar op de prerelease site. Deze worden bij uitzondering vast opgenomen in de Nederlandse Labcodeset, wanneer deze codes urgent nodig zijn (zoals bijvoorbeeld in de COVID-19-pandemie). Deze codes zijn niet beschikbaar in de LOINC FHIR-resources, en kunnen daarom ook niet in onze transformatie gebruikt worden. CodeSystem supplements kunnen geen codes toevoegen aan een bestaand CodeSystem, dus is er op dit moment geen goede manier om een LOINC pre-release code in de labcodeset via FHIR beschikbaar te maken. Bij de huidige, meest recente versie van de Labcodeset gecombineerd met de nieuwste LOINC-versie is dit niet aan de orde. We zijn in gesprek met Regenstrief voor een oplossing. Het is zeer onwaarschijnlijk dat de oplossing voor dit probleem _breaking changes_ gaat opleveren in de huidige transformatie van de labcodeset, dus hoeft implementatie niet uitgesteld te worden.

## Beschikbare FHIR-resources
Om de labcodeset voldoende in FHIR uit te drukken zijn de volgende FHIR resources nodig gebleken:
| Type | URI | Description |
| ---- | --- | ----------- |
| CodeSystem supplement | http://labterminologie.nl/cs/labconcepts | CodeSystem supplement containing Dutch translations and properties as defined in the Labcodeset XML file |
| ValueSet | http://labterminologie.nl/vs/labconcepts | ValueSet containing the unique set of LOINC codes referenced by the Labcodeset XML file - note this contains LOINC codes in any state, not just active codes |
| CodeSystem fragment | http://unitsofmeasure.org | Fragment of the UCUM CodeSystem containing the union of UCUM expressions referenced by Labcodeset and the ["common" FHIR UCUM ValueSet](https://www.hl7.org/fhir/valueset-ucum-common.html). Language for this CodeSystem is nl_NL, and Dutch display terms are used where available with the English terms set as designations |
| ValueSet | http://labterminologie.nl/vs/labconcepts-ucum | ValueSet containing the unique set of UCUM expressions referenced by Labcodeset |
| ConceptMap | http://labterminologie.nl/cm/labconcepts-ucum | ConceptMap containing mappings for each LOINC code in Labcodeset where a unit is expressed |
| ValueSet | http://labterminologie.nl/vs/labconcepts-materials | ValueSet containing the unique set of SNOMED CT "material" codes referenced by Labcodeset |
| ConceptMap | http://labterminologie.nl/cm/labconcepts-materials | ConceptMap containing mappings from LOINC codes in Labcodeset that reference one or more SNOMED CT "material" codes |
| ValueSet | http://labterminologie.nl/vs/labconcepts-ordinal-xxx | A set of ValueSets, one for each "ordinal" list included in the Labcodeset XML file where xxx in the URI is replaced with the OID of the ordinal set (ie. 2.16.840.1.113883.2.4.3.46.99.7.11.1.6, 2.16.840.1.113883.2.4.3.46.99.7.11.1.11, 2.16.840.1.113883.2.4.3.46.99.7.11.1.13 or 2.16.840.1.113883.2.4.3.46.99.7.11.1.14) |
| ConceptMap | http://labterminologie.nl/cm/labconcepts-outcomes | ConceptMap containing mappings from LOINC codes in Labcodeset to either a SNOMED CT Reference Set identifier or a ValueSet OID depending upon which is listed in Labcodeset for that LOINC code.

## Gebruik
De complexe structuur van de Nederlanse Labcodeset leidt ertoe, dat er regelmatig meerdere operaties nodig zijn om de juiste informatie op te halen. CSIRO heeft een reeks voorbeelden met postman gemaakt die laten zien hoe de informatie die nodig is voor een bepaalde usecase op te halen is met de Terminologieserver. U kunt daarbij denken aan het ophalen van alle LOINC-concepten in de Nederlandse Labcodeset; de SNOMED-materialen die gekoppeld zijn aan een specifiek LOINC-concept; de UCUM-eenheid die gekoppeld is aan een specifiek LOINC-concept; enzovoort. U vindt deze voorbeelden in de postman-collectie elders in deze repository: https://github.com/terminologieserver/postman_voorbeelden.