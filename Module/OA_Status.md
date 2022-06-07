# Open Access Status

Die Abfrage an Unpaywall erfolgt, um den OA-Status einer Publikation zu ermitteln und ggf. andere auf Repositorien verfügbare Versionen zu identifizieren, die für eine Zweitveröffentlichung auf DepositOnce nachgenutzt werden könnten.

> Daten werden im JSON-Format ausgegeben.
100.000 Abfragen am Tag an die REST-Schnittstelle sind frei. Eine Registrierung ist nicht erforderlich, jedoch soll bei jeder Abfrage die E-Mail-Adresse übermittelt werden.
Beispielanfragen:
OA-Artikel in OA-Zeitschrift https://api.unpaywall.org/v2/10.1371/journal.pone.0198249?email=INSERT@YOUR.EMAIL
OA-Version via Repository https://api.unpaywall.org/v2/10.1080/00987913.2008.10765150?email=INSERT@YOUR.EMAIL
Verschiedene OA-Versionen via Repositories: https://api.unpaywall.org/v2/10.1038/nature12373?email=INSERT@YOUR.EMAIL

## UPW

* DOI -> Edit column -> Add column by fetching URLs -> UPW
* Wichtig: Throttle delay 1000 ms
* Wichtig: On Error: store error

```
if(isNotNull(value),
  'https://api.unpaywall.org/v2/' + value + '?email=openaccess@ub.uni-frankfurt.de',
  null
)
```

## OA-Status

Es wird das Unpaywall-Feld is_oa ausgelesen und der darin enthaltene Boolesche Wert in einer neuen Spalte UPW: IS OA? gespeichert.
Es sind also zwei Rückgabewerte möglich:
1. "true" -> Unpaywall konnte eine OA verfügbare Variante zuordnen
1. "false" -> Unpaywall konnte keine OA verfügbare Variante zuordnen
* UPW -> Edit column -> Add column based on this column -> UPW: IS OA?
```
value.parseJson().is_oa
```

## Status Zeitschrift

Es wird das Unpaywall-Feld journal_is_in_doaj ausgelesen – also die Angabe dazu, ob es sich um eine reine OA-Zeitschrift handelt, die im Directory of Open Access Journals (DOAJ) gelistet ist – und der darin enthaltene Boolesche Wert in einer neuen Spalte UPW: J DOAJ? gespeichert.

* UPW -> Edit column -> Add column based on this column -> UPW: J DOAJ?
```
value.parseJson().journal_is_in_doaj
```

## Beste OA Variante

Unpaywall identifiziert mitunter verschiedene OA-Varianten eines Artikels; nach eigenen Vorgaben wird die "beste" OA-Variante identifizert und in dem (verschachtelten) Unpaywall-Feld best_oa_location angegeben.
Einzelne Subfelder des Unpaywall-Feldes best_oa_location werden ausgelesen und in einer neuen Spalte UPW: bestOA gespeichert.
Die ausgewählten Subfelder werden in der folgenden Reihenfolge und Form ausgegeben: "Evidence: evidence -- URL: url -- Host Type: host_type -- License: license"

* UPW -> Edit column -> Add column based on this column -> UPW: best OA
```
'Evidence: ' + value.parseJson()['best_oa_location'].evidence
+ ' -- URL: ' + value.parseJson()['best_oa_location'].url
+ ' -- Host Type: ' + value.parseJson()['best_oa_location']['host_type']
+ ' -- License: ' + value.parseJson()['best_oa_location']['license']
```

## OA Versionen über Repositorien

Unpaywall identifiziert mitunter verschiedene OA-Varianten eines Artikels und listet relevante Angaben zu allen Varianten in dem (verschachtelten) Unpaywall-Feld oa_locations.
An dieser Stelle sollen die OA-Varianten, die über OA-Repositorien verfügbar sind, identifiziert werden.

* UPW -> Edit column -> Add column based on this column -> UPW: OA-Repos

```
forEach(value.parseJson()['oa_locations'], v,
  if(v['host_type'] == 'repository',
    'Landing page: ' + v['url_for_landing_page']
    + ' -- PDF-Link: ' + v['url_for_pdf']
    + ' -- License: ' + v['license']
    + ' -- version: ' + v['version'],
     
    null
    )
).uniques().join(' ; ')
```