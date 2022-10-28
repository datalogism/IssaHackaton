# Hackaton ISSA


* page projet : 
    * https://github.com/issa-project/
    * https://issa.cirad.fr/
* page données : https://zenodo.org/record/6505847#.Y0HLGTVBxFR
* endpoint SPARQL : https://data-issa.cirad.fr/sparql

## Docker corese

https://hub.docker.com/r/wimmics/corese

## How to retrain a word2vec ?
https://www.quora.com/Can-we-re-train-a-word-embedding-model-with-additional-data


## Extraction de termes

* Indice de spécificité de Lafon  : Indémodable !
https://www.persee.fr/doc/mots_0243-6450_1980_num_1_1_1008
* Utilisé pour construction thésaurus par ISTEX et INRAE  : http://termsuite.github.io/#publications
* KeyBert > https://github.com/MaartenGr/KeyBERT#embeddings


## Embeddings :

* Multilingual : https://github.com/facebookresearch/MUSE
* Learning Hyperonyms from Ontology :  https://www.ncbi.nlm.nih.gov/pmc/articles/PMC8610673/

## Extraction de relation sémantiques

L'idée serai de pouvoir repérer des relation d'hyperonimie
https://medium.com/analytics-vidhya/automatic-extraction-of-hypernym-relations-from-text-using-ml-4b04eb33097f
https://github.com/suhaibani/HWE

## Analyse de semantic shift et modèles diachroniques

 * Comparer des embeddings sur plusieurs périodes
https://aclanthology.org/2021.naacl-main.369/
https://www.changeiskey.org/talk/kbr-digital-heritage-seminar-series/2022-10-18-KBR-for%20publication-combined.pdf


HistBERT : 
https://www.connectedpapers.com/main/1cee062839bf769565bc64df1d7842ff0b223f3d/HistBERT%3A-A-Pre%20trained-Language-Model-for-Diachronic-Lexical-Semantic-Analysis/graph

## First queries 

``` 
SELECT DISTINCT ?d WHERE { 
GRAPH <http://data-issa.cirad.fr/graph/articles> {
?s dct:issued ?d
}} order by ?d
```

```
prefix issapr: <http://data-issa.cirad.fr/property/>

SELECT DISTINCT ?d WHERE { 
GRAPH <http://data-issa.cirad.fr/graph/articles> {
?s dct:issued ?d; issapr:hasBody ?body.
}} order by ?d
```

```

prefix issapr: <http://data-issa.cirad.fr/property/>
SELECT DISTINCT ?d COUNT(?s)  WHERE { 
GRAPH <http://data-issa.cirad.fr/graph/articles> {
?s dct:issued ?d; issapr:hasBody ?body.
}} order by ?d
```

## GET ABSTRACTS

``` 
refix issapr: http://data-issa.cirad.fr/property/

SELECT * WHERE { 
GRAPH http://data-issa.cirad.fr/graph/articles {
?s dct:issued ?d; dct:title  ?title . 
?abstract http://purl.org/vocab/frbr/core#partOf ?s; rdf:type http://purl.org/spar/fabio/Abstract .
?abstract rdf:value ?AbstractValue . 
}} 
LIMIT 100
```
```
prefix issapr: <http://data-issa.cirad.fr/property/>
prefix dct:	<http://purl.org/dc/terms/>
prefix fabio:     <http://purl.org/spar/fabio/>
prefix frbr: <http://purl.org/vocab/frbr/core#>
SELECT * WHERE { 
GRAPH <http://data-issa.cirad.fr/graph/articles> {
?s dct:issued ?d; dct:title  ?title . 
?abstract frbr:partOf ?s; rdf:type fabio:Abstract .
?abstract rdf:value ?AbstractValue . 
}} 
LIMIT 100
```

---------------

## WORK DAY 1

```
prefix issapr: <http://data-issa.cirad.fr/property/>
prefix dct:	<http://purl.org/dc/terms/>
prefix fabio:     <http://purl.org/spar/fabio/>
prefix frbr: <http://purl.org/vocab/frbr/core#>
prefix issa: <http://data-issa.cirad.fr/>
prefix oa:     <http://www.w3.org/ns/oa#>
prefix prov:   <http://www.w3.org/ns/prov#>

SELECT * WHERE { 
?desc a issa:ThematicDescriptorAnnotation;
prov:wasAttributedTo ?annoType;
oa:hasTarget ?paper;
oa:hasBody <http://aims.fao.org/aos/agrovoc/c_7713>.
?paper dct:identifier ?paperID;
             dc:language ?lang
}
```

https://agrovoc.fao.org/sparql

``` 
PREFIX skos: <http://www.w3.org/2004/02/skos/core#> 
PREFIX skosxl: <http://www.w3.org/2008/05/skos-xl#> 
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#> 
SELECT DISTINCT ?concept ?lang ?prefLabel (GROUP_CONCAT ( DISTINCT concat('"',?altLabel,'"@',lang(?altLabel)); separator="|_|" ) as ?altLabels) 
WHERE { 
 
  ?concept skosxl:prefLabel/skosxl:literalForm ?prefLabel . 
  BIND(lang(?prefLabel) AS ?lang) 
  OPTIONAL{ 
  	?concept skosxl:altLabel/skosxl:literalForm ?altLabel . 
  	FILTER(lang(?altLabel) = ?lang) 
  }.
  FILTER( CONTAINS(?prefLabel,"cacao") )
} 
GROUP BY ?concept ?lang ?prefLabel 
ORDER BY ?lang 
```
```
prefix issapr: <http://data-issa.cirad.fr/property/>
prefix dct:	<http://purl.org/dc/terms/>
prefix fabio:     <http://purl.org/spar/fabio/>
prefix frbr: <http://purl.org/vocab/frbr/core#>
prefix issa: <http://data-issa.cirad.fr/>
prefix oa:     <http://www.w3.org/ns/oa#>
prefix prov:   <http://www.w3.org/ns/prov#>
prefix skosxl: <http://www.w3.org/2008/05/skos-xl#>

SELECT DISTINCT ?desc2 ?prefLabel WHERE 
{
{
SELECT 
DISTINCT ?paper
 WHERE { 
?paper dct:identifier ?paperID;
             dc:language ?lang.

?desc a issa:ThematicDescriptorAnnotation;
prov:wasAttributedTo ?annoType;
oa:hasTarget ?paper;
oa:hasBody ?entity.
 ?desc oa:hasBody <http://aims.fao.org/aos/agrovoc/c_7713>
}}.
?desc oa:hasTarget ?paper;
 oa:hasBody ?desc2.
?desc2 skosxl:prefLabel/skosxl:literalForm ?prefLabel
}
```

```
prefix issapr: <http://data-issa.cirad.fr/property/>
prefix dct:	<http://purl.org/dc/terms/>
prefix fabio:     <http://purl.org/spar/fabio/>
prefix frbr: <http://purl.org/vocab/frbr/core#>
prefix issa: <http://data-issa.cirad.fr/>
prefix oa:     <http://www.w3.org/ns/oa#>
prefix prov:   <http://www.w3.org/ns/prov#>
prefix skosxl: <http://www.w3.org/2008/05/skos-xl#>


SELECT 
DISTINCT ?paper
 WHERE { 
?paper dct:identifier ?paperID;
             dc:language ?lang.

?desc a issa:ThematicDescriptorAnnotation;
prov:wasAttributedTo ?annoType;
oa:hasTarget ?paper;
oa:hasBody ?entity.
 ?desc oa:hasBody ?concept.
?concept skosxl:prefLabel/skosxl:literalForm ?prefLabel . 
OPTIONAL{ 
  	?concept skosxl:altLabel/skosxl:literalForm ?altLabel .
  }.
  FILTER( CONTAINS(?prefLabel,"cacao") || CONTAINS(?altLabel,"cacao"))

}
```
```
prefix issapr: <http://data-issa.cirad.fr/property/>
prefix dct:	<http://purl.org/dc/terms/>
prefix fabio:     <http://purl.org/spar/fabio/>
prefix frbr: <http://purl.org/vocab/frbr/core#>
prefix issa: <http://data-issa.cirad.fr/>
prefix oa:     <http://www.w3.org/ns/oa#>
prefix prov:   <http://www.w3.org/ns/prov#>
prefix skosxl: <http://www.w3.org/2008/05/skos-xl#>


SELECT 
DISTINCT ?paper ?AbstractValue
 WHERE { 
?paper dct:identifier ?paperID;
             dc:language ?lang.
?abstract frbr:partOf ?paper; rdf:type fabio:Abstract .
?abstract rdf:value ?AbstractValue . 

?desc a issa:ThematicDescriptorAnnotation;
prov:wasAttributedTo ?annoType;
oa:hasTarget ?paper;
oa:hasBody ?entity;
oa:hasBody <http://aims.fao.org/aos/agrovoc/c_7713>

}
```
