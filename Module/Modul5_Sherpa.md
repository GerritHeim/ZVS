# Sherpa Romeo

Dokumentation API: https://v2.sherpa.ac.uk/api/

API-Call-URL hat folgendes Schema
 
`https://v2.sherpa.ac.uk/cgi/retrieve_by_id/cgi/retrieve_by_id?item-type=publication&api-key=UNSER_API_KEY&format=Json&identifier=ISSN`

Die Sherpa API gibt ein verschachteltes JSON-Ergebnis aus. Je nach Zeitschrift können unterschiedliche Wege für die OA-ZV vorgesehen sein. Die Ausgabe mit nur einem Weg (Beispiel Nomos) oder mit Pathway A und Pathway B (Beispiel Sage). Es gibt deshalb zwei Ermittlungen der möglichen Weg. Existiert nur ein Weg, erfolgt die Ausgabe in Pathway A.

## Sherpa Romeo Daten beziehen
Zuerst die Daten von Sherpa Romeo über die ISSN beziehen.

* ISSN -> Edit column -> Add column by fetching URLs ... -> SR_ISSN

und

* eISSN -> Edit column -> Add column by fetching URLs ... -> SR_eISSN

**Trottle Delay auf 1000 setzen**

```
if(isNotNull(value),
  'https://v2.sherpa.ac.uk/cgi/retrieve_by_id/cgi/retrieve_by_id?item-type=publication&api-key=91F7D03A-22C4-11EC-AE82-7916BE23C491&format=Json&identifier=' + value,
  null
)
```

* SR_ISSN → Edit column → Join columns

Beide Spalten auswählen
Write result in new column named SR
Delete joined columns

Nächste Schritte sind die Aufsplittung der erhaltenen JSON Ausgabe in die relevanten Bestandteile.

## Pathway A

* SR-> Edit column -> Add column based on this column -> SR_LOCATION_PATH_A

`value.parseJson().items[0].publisher_policy[0].permitted_oa[0].location.location.join("; ")`

* SR-> Edit column -> Add column based on this column -> SR_VERSION_PATH_A

`value.parseJson().items[0].publisher_policy[0].permitted_oa[0].article_version.join("; ")`

* SR-> Edit column -> Add column based on this column -> SR_CONDITION_PATH_A

`value.parseJson().items[0].publisher_policy[0].permitted_oa[0].conditions.join("; ")`

* SR-> Edit column -> Add column based on this column -> SR_EMBARGO_PATH_A

`value.parseJson().items[0].publisher_policy[0].permitted_oa[0].embargo.amount`

* SR_LOCATION_PATH_A→ Edit column → Join columns

Alle in PATH A angelegten Spalten auswählen.
Separator: |
Write result in new column named SR_PATH_A
Delete joined columns

## Pathway B

* SR-> Edit column -> Add column based on this column -> SR_LOCATION_PATH_B

`value.parseJson().items[0].publisher_policy[0].permitted_oa[1].location.location.join("; ")`

* SR --> Edit column -> Add column based on this column → SR_VERSION_PATH_B

`value.parseJson().items[0].publisher_policy[0].permitted_oa[1].article_version.join("; ")`
 * SR-> Edit column -> Add column based on this column -> SR_CONDITION_PATH_B

`value.parseJson().items[0].publisher_policy[0].permitted_oa[1].conditions.join("; ")`
 * SR-> Edit column -> Add column based on this column -> SR_EMBARGO_PATH_B

`value.parseJson().items[0].publisher_policy[0].permitted_oa[1].embargo.amount`

* SR_LOCATION_PATH_B → Edit column → Join columns

Alle in PATH A angelegten Spalten auswählen.
Separator: | 
Write result in new column named SR_PATH_B
Delete joined columns
URLs zu weiterführenden Informationen

* SR-> Edit column -> Add column based on this column -> SR_POLICY_URLs

`value.parseJson().items[0].publisher_policy[0].urls[0].url`

* SR-> Edit column -> Add column based on this column -> SR_URI

`value.parseJson().items[0].system_metadata.uri`

ggf. diesen Ablauf für weitere Pathways wiederholen.