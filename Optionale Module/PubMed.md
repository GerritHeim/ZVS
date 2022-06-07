# PubMed

## PBMED ID
Es wird eine Anfrage an die ID Converter API auf Basis der DOI gestellt und die Antwort in der (neuen) Spalte PM_CONVERTER_API gespeichert.
Die ID Converter API gibt Daten in verschiedenen Formaten aus: der Standard ist XML, sofern keine anderes Format angefragt wird, mit Parameter &format=json erfolgt die Rückgabe im JSON-Format.
* DOI -> edit column -> Add column by fetching URLs -> PM_CONVERTER_API
* Wichtig: Throttle delay 1000 milliseconds

`'https://www.ncbi.nlm.nih.gov/pmc/utils/idconv/v1.0/?tool=ZVSUBJCSn&email=mail@domain.td&format=json&ids=' + value`

Aus der JSON Ausgabe muss die ID herausgefilter werden:

* PM_CONVERTER_API -> Edit column -> Add column based on this column -> IdentifierPubmed

```
forEach(value.parseJson().records,v,
  v.pmid
).uniques().join('; ')
```

## Abstract
Es wird eine Anfrage an die eSummary API auf Basis der PubMed-ID gestellt und die Antwort in der (neuen) Spalte PM_MD_API gespeichert.

* IdentifierPubmed -> Edit column -> Add column by fetching URLs -> PM_MD_API
* Wichtig: Throttle delay 1000 milliseconds

```
if(isNonBlank(value),
  'https://eutils.ncbi.nlm.nih.gov/entrez/eutils/efetch.fcgi?db=pubmed&retmode=xml&rettype=abstract&id=' + value.replace('; ', ','),
  null
)
PM_MD_API -> Edit column -> Add column based on this column -> PM_abstract

forEach(value.parseHtml().select('abstracttext'),v,
  v.htmlText()
).uniques().join('; ')
```