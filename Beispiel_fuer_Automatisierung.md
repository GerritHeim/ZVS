# Beispiel für Automatisierung
OpenRefine besitzt in der linken Seitenleiste im Reiter **Undo/Redo** die Schaltflächen **Extract** und **Apply**. Über Extract lässt sich der vergangene Arbeitsablauf exportieren, über Apply ein solcher Export auf die vorhandenen Daten anwenden.

**Wichtig: Die Tabelle muss exakt die Spalten haben, über welche die Vorlage verfügte, bei der per Extract der Arbeitsverlauf entommen wurde!**

Ein Verlauf sieht wie folgt aus (Beispiel Unpaywall-Modul)

```
[
  {
    "op": "core/column-addition-by-fetching-urls",
    "engineConfig": {
      "facets": [],
      "mode": "row-based"
    },
    "baseColumnName": "DOI",
    "urlExpression": "grel:if(isNotNull(value),\n  'https://api.unpaywall.org/v2/' + value + '?email=openaccess@ub.uni-frankfurt.de',\n  null\n)",
    "onError": "store-error",
    "newColumnName": "UPW",
    "columnInsertIndex": 3,
    "delay": 1000,
    "cacheResponses": true,
    "httpHeadersJson": [
      {
        "name": "authorization",
        "value": ""
      },
      {
        "name": "user-agent",
        "value": "OpenRefine 3.4.1 [437dc4d]"
      },
      {
        "name": "accept",
        "value": "*/*"
      }
    ],
    "description": "Create column UPW at index 3 by fetching URLs based on column DOI using expression grel:if(isNotNull(value),\n  'https://api.unpaywall.org/v2/' + value + '?email=openaccess@ub.uni-frankfurt.de',\n  null\n)"
  },
  {
    "op": "core/column-addition",
    "engineConfig": {
      "facets": [],
      "mode": "row-based"
    },
    "baseColumnName": "UPW",
    "expression": "grel:value.parseJson().is_oa",
    "onError": "set-to-blank",
    "newColumnName": "UPW: IS OA?",
    "columnInsertIndex": 4,
    "description": "Create column UPW: IS OA? at index 4 based on column UPW using expression grel:value.parseJson().is_oa"
  },
  {
    "op": "core/column-addition",
    "engineConfig": {
      "facets": [],
      "mode": "row-based"
    },
    "baseColumnName": "UPW",
    "expression": "grel:value.parseJson().journal_is_in_doaj",
    "onError": "set-to-blank",
    "newColumnName": "UPW: J DOAJ?",
    "columnInsertIndex": 4,
    "description": "Create column UPW: J DOAJ? at index 4 based on column UPW using expression grel:value.parseJson().journal_is_in_doaj"
  },
  {
    "op": "core/column-addition",
    "engineConfig": {
      "facets": [],
      "mode": "row-based"
    },
    "baseColumnName": "UPW",
    "expression": "grel:'Evidence: ' + value.parseJson()['best_oa_location'].evidence\n+ ' -- URL: ' + value.parseJson()['best_oa_location'].url\n+ ' -- Host Type: ' + value.parseJson()['best_oa_location']['host_type']\n+ ' -- License: ' + value.parseJson()['best_oa_location']['license']",
    "onError": "set-to-blank",
    "newColumnName": "UPW: best OA",
    "columnInsertIndex": 4,
    "description": "Create column UPW: best OA at index 4 based on column UPW using expression grel:'Evidence: ' + value.parseJson()['best_oa_location'].evidence\n+ ' -- URL: ' + value.parseJson()['best_oa_location'].url\n+ ' -- Host Type: ' + value.parseJson()['best_oa_location']['host_type']\n+ ' -- License: ' + value.parseJson()['best_oa_location']['license']"
  },
  {
    "op": "core/column-addition",
    "engineConfig": {
      "facets": [],
      "mode": "row-based"
    },
    "baseColumnName": "UPW",
    "expression": "grel:forEach(value.parseJson()['oa_locations'], v,\n  if(v['host_type'] == 'repository',\n    'Landing page: ' + v['url_for_landing_page']\n    + ' -- PDF-Link: ' + v['url_for_pdf']\n    + ' -- License: ' + v['license']\n    + ' -- version: ' + v['version'],\n    \n    null\n    )\n).uniques().join(' ; ')",
    "onError": "set-to-blank",
    "newColumnName": "UPW: OA-Repos",
    "columnInsertIndex": 4,
    "description": "Create column UPW: OA-Repos at index 4 based on column UPW using expression grel:forEach(value.parseJson()['oa_locations'], v,\n  if(v['host_type'] == 'repository',\n    'Landing page: ' + v['url_for_landing_page']\n    + ' -- PDF-Link: ' + v['url_for_pdf']\n    + ' -- License: ' + v['license']\n    + ' -- version: ' + v['version'],\n    \n    null\n    )\n).uniques().join(' ; ')"
  }
]
```