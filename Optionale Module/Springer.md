# Springer

Für die API ist ein Account und ein darüber erzeugter API-Key notwendig:
Dashboard: https://dev.springernature.com

## Abstract
Die Abfrage bei Springer funktioniert nur für Publikationen von Springer. Die Abfrage auf Basis der DOI filter deshalb die DOI Präfixe von Springer. Änderungen müssen hier ggf. nachvollzogen werden.

* DOI -> Edit cloumn -> Add column by fetching URLs... -> SPRINGER

```
if('10.33283 10.1617 10.1557 10.1245 10.1251 10.3858 10.1208 10.1114
    10.3758 10.1186 10.1140 10.1361 10.1379 10.1381 10.1385 10.1007
    10.1013 10.1065 10.1023 10.1038 10.1057 10.5819 10.5052 10.26778
    10.26777 10.7603 10.7123 10.5052 10.2165 10.19150 10.4333 10.4056
    10.17269'.contains(value.split('/')[0]),
  'http://api.springernature.com/metadata/json?q=doi:' + value + '&api_key=91d251c30eec41c55a42f6af4e61ce1b',
  null
)
```
(Liste der DOIs abgeglichen Stand 01.03.2022)

Sofern in den Springer Nature-Daten ein Abstract vorhanden ist, wird dieses in der Spalte TitleAbstract gespeichert und der ggf. bereits vorhandene Wert (via Crossref, PubMed, arXiv oder CORE) überschrieben.

* TitleAbstract -> Edit cells -> Transform...
* Wichtig: Language: Python / Jython

```
import json
 
j = json.loads(cells.SPRINGER.value)
records = j.get('records', [])
 
# Set mit allen Abstracts -> Set = Array, in dem jedes Element nur einmal vorkommen kann -> keine Dubletten
abstracts = set([i.get('abstract', '') for i in records])
 
# Viele (alle?) englischsprachige Abstracts beginnen mit 'Abstract', das sollen sie nicht.
abstracts = [i[8:] if i.startswith('Abstract') else i for i in abstracts]
# Viele (alle?) deutschsprachige Abstracts beginnen mit 'Zusammenfassung', das sollen sie nicht.
abstracts = [i[15:] if i.startswith('Zusammenfassung') else i for i in abstracts]
 
if abstracts:
    return '\n\n'.join(abstracts)
```