# Human Phenotype Ontology
## Auteurs
__Geschreven door__: Sander Mertens

__Review door__: Michael Verbeek

## Inleiding
In dit document wordt de werkwijze omtrent het uploaden van de __Human Phenotype Ontology__ gedocumenteerd. Het doel van dit document is inzichtelijk maken welke keuzes er gemaakt zijn voor dit proces, welke wijzigingen er ten opzichte van de bron gemaakt worden, en wat de aandachtspunten bij het gebruik van deze resources zijn.

## Methode
- De release files van de HPO worden naar FHIR-formaat getransformeerd middels de methode beschikbaar op https://github.com/mertenssander/hpo_to_fhir. Hieruit volgt 1 resource: het _HPO_ codesystem.
- Bij het uploaden wordt een meta security label (https://www.hl7.org/fhir/security-labels.html) toegevoegd, waarmee toegang tot de resource beperkt kan worden tot een groep gebruikers. Dit label is voor HPO 'hpo.read'.
- Aan de codesystems wordt een valueSet key toegevoegd. Dit is een url voor een implicit valueset die het hele codesysteem omvat. Deze wordt gegenereerd volgens het format: {{url}}?vs. Met andere woorden: er wordt *?vs* achter de URL geplaatst. Hiermee is het mogelijk om de server aan te roepen met https://terminologieserver.nl/fhir/ValueSet/$expand?url={{url}}?vs , met alle mogelijke parameters voor een $expand request.

## Aandachtspunten bij het gebruik van HPO via de Terminologieserver
- De URI van HPO is niet vastgelegd in de FHIR TA (https://confluence.hl7.org/display/TA/HPO+The+Human+Phenotype+Ontology). Er is geen consensus, derhalve wordt de URI uit het OWL-bestand aangehouden. Een deel van de standpunten is terug te lezen in deze discussie: https://chat.fhir.org/#narrow/stream/179202-terminology/topic/How.20to.20represent.20multiple.20inheritance.20in.20a.20CodeSystem
- Versies van de HPO worden uitgedrukt volgens het formaat __http://purl.obolibrary.org/obo/hp/releases/YYYY-MM-DD/hp.owl__. Dit formaat kan Ontoserver niet interpreteren, dus voor het ophalen van de laatste versie moet de versie volgens dit formaat worden meegestuurd met de request.
- Xref informatie uit de ontologie wordt beschikbaar gesteld als properties met de naam __xref__.
- Superclass relaties worden als __parent__ properties beschikbaar gemaakt.
- Subclass relaties worden als __child__ properties beschikbaar gemaakt.
- Alternatieve weergaveteksten worden toegevoegd als __designation__ indien de scope van het synoniem EXACT is.

## Beschikbare FHIR-resources
De gegenereerde CodeSystems hebben de volgende URIs:

|Type|Titel|URI|
|:---|:---|:---|
|CodeSystem|HPO|http://purl.obolibrary.org/obo/hp.owl|

## Schema updates
- Middels een geautomatiseerd proces wordt minimaal eenmaal per week de meest recente versie van de HPO gedownload. Vervolgens wordt de transformatie uitgevoerd zoals beschreven onder _methode_.
