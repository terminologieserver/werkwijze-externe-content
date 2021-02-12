# SNOMED
## Auteurs
__Geschreven door__: Feikje Hielkema-Raadsveld, functioneel beheerder Nederlandse Labcodeset

__Review door__: Sander Mertens, projectleider Terminologieserver

## Inleiding
In dit document wordt de werkwijze omtrent het uploaden van de Nederlandse Labcodeset gedocumenteerd. Het doel van dit document is inzichtelijk maken welke keuzes er gemaakt zijn voor dit proces, welke wijzigingen er ten opzichte van de bron gemaakt worden, en wat de aandachtspunten bij het gebruik van deze resources zijn.

## Methode
- Bij het verschijnen van een nieuwe versie van de Nederlandse Labcodeset wordt deze geïndexeerd middels een applicatie die ontwikkeld is voor Ontoserver door CSIRO.
- <span style="color:blue">Bij het uploaden wordt een meta security label (https://www.hl7.org/fhir/security-labels.html) toegevoegd, waarmee toegang tot de resource beperkt kan worden tot een groep gebruikers. Aangezien de Labcodeset gebaseerd is op LOINC, UCUM en SNOMED, en alleen voor die laatste een licentie vereist is, is dit label voor de Labcodeset 'snomed.read'.</span>

## Aandachtspunten bij het gebruik van de Nederlandse Labcodeset via de Terminologieserver
- De XML-versie van de Nederlandse Labcodeset kan codes uit de LOINC prerelease bevatten: concepten die al wel aangemaakt, maar nog niet gepubliceerd zijn door LOINC. Op dit moment worden prerelease-codesnog niet ondersteund door de Terminologieserver. Deze codes ontbreken dus op de Terminologieserver.
- De Terminologieserver biedt nog geen ondersteuning voor LOINC panels. De panelconcepten zijn wel vindbaar in de Terminologieserver, maar de informatie welke concepten lid van het panel zijn is nog niet beschikbaar.
- __Versies__ Nieuwe versies worden als een nieuwe business version geüpload. Hierdoor blijft de vorige versie ook toegankelijk. Het streven is om naast de nieuwste versie __1__ vorige versie aan te bieden.

## Schema updates
- Bij het verschijnen van een nieuwe editie van de Nederlandse Labcodeset zal de nieuwe versie zo spoedig mogelijk verwerkt worden in de Terminologieserver. 
- Bij het verschijnen van een nieuwe editie van LOINC zal de nieuwe versie ook zo spoedig mogelijk verwerkt worden in de Terminologieserver. Dit kan gevolgen hebben voor de Nederlandse vertalingen van de LOINC-concepten. Ook kunnen prerelease-concepten beschikbaar komen, omdat ze inmiddels gepubliceerd zijn.

## Ontwerp
De Nederlandse Labcodeset is niet een op zichzelf staand codestelsel, maar een combinatie van verschillende internationale terminologiestelsels: LOINC, SNOMED en UCUM. De wetenschappelijke verenigingen NVKC en NVMM hebben een subset van LOINC gekozen, voorzien van verbanden met SNOMED (materialen, uitkomsten) en UCUM (eenheid) en Nederlandse informatie (vertalingen, lokale panels) toegevoegd. Zowel de internationale als de Nederlandse versies van die standaarden beschouwen we als aparte FHIR resources en supplementen. De Labcodeset heeft vorm gekregen als een aantal Value Sets hierop. Om de Labcodeset beschikbaar te maken met de FHIR Terminologieserver, zijn de volgende FHIR resources nodig gebleken:
- Een LOINC CodeSystem (internationale versie)
- Een CodeSystem supplement voor LOINC-nl, met daarin Nederlandse vertalingen van LOINC-concepten en hun eigenschappen
- Een CodeSystem met de Nederlandse SNOMED-editie: zie https://github.com/terminologieserver/werkwijze-externe-content/blob/main/snomed.md
- Een UCUM CodeSystem (internationale versie)
- Een CodeSystem fragment met daarin de Nederlandse vertalingen van de UCUM-codes die in de Nederlandse Labcodeset vertaald zijn
- Een ValueSet met alle LOINC-concepten die actief zijn in de Nederlandse Labcodeset
- Een ValueSet met alle LOINC-concepten die lid zijn (dus ook inactieve) van de Nederlandse Labcodeset
- Een ValueSet met alle UCUM-expressies die in de Nederlandse Labcodeset gebruikt worden
- Een ValueSet met alle SNOMED-materialen (specimen) die in de Nederlandse Labcodeset gebruikt worden
- Aparte ValueSets voor elke lijst met ordinale uitkomsten in de Nederlandse Labcodeset
- Een ConceptMap die elk LOINC-concept mapt op de SNOMED-materialen zoals gedefinieerd in de Nederlandse Labcodeset
- Een ConceptMap die elk kwalitatief LOINC-concept mapt op de SNOMED-uitkomsten zoals gedefinieerd in de Nederlandse Labcodeset
- Een ConceptMap die elk kwantitatief LOINC-concept mapt op een UCUM-expressie zoals gedefinieerd in de Nederlandse Labcodeset.

## Gebruik
De complexe structuur van de Nederlanse Labcodeset leidt ertoe, dat er regelmatig meerdere operaties nodig zijn om de juiste informatie op te halen. CSIRO heeft een reeks voorbeelden met postman gemaakt die laten zien hoe de informatie die nodig is voor een bepaalde usecase op te halen is met de Terminologieserver. U kunt daarbij denken aan het ophalen van alle LOINC-concepten in de Nederlandse Labcodeset; de SNOMED-materialen die gekoppeld zijn aan een specifiek LOINC-concept; de UCUM-eenheid die gekoppeld is aan een specifiek LOINC-concept; enzovoort. U vindt deze voorbeelden in de postman-collectie elders in deze repository <span style="color:blue">(https://github.com/terminologieserver/postman_voorbeelden).</span>