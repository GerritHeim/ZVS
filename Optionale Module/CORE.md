# CORE

> Es ist ein API-Key notwendig, der verab registriert werden muss.

Zuerst werden die Daten gesammelt von CORE abgeholt und anschließend aufgespalten.

* DOI -> Edit column -> Add column by fetching URLs -> CORE

`'https://core.ac.uk/api-v2/articles/search/doi:"' + value + '"?apiKey=KEY'`

Als nächstes wird das CORE-Feld description ausgelesen und Abstracts werden in der neuen Spalte CORE_abstract gespeichert.

* CORE -> Edit column -> Add column based on this column -> CORE_abstract

```
forEach(value.parseJson().data, v,
  v.description
).uniques().join(';')
```

Nun wird noch der Volltext-Download ausgelesen. Das Feld enthält URLs zum Direktdownload von Volltexten bei CORE, denn CORE harvestet Metadaten und frei zugängliche Volltexte von Repositorien und Journalen.

* CORE -> Edit column -> Add column based on this column -> CORE_downloadUrl

```
forEach(value.parseJson().data, v,
  v.downloadUrl
).uniques().join('; ')
```