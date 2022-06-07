# Zeitschriften ohne DOI
## Citavi-Export für OpenRefine vorbereiten
Tabellenansicht öffnen mit STRG + ALT + T → Export in Excel. Hier zusätzlich beim ersten Mal über Ansicht → Spalten die Spalten für ISSN und eISSN einblenden. Ggf. auch Spalte für das Feld, in dem nicht benötigte Sammelwerke markiert wurden, einsetzen.

Anschließend in Excel die ersten 2 Spalten löschen, weil OpenRefine sich an diesen "verschluckt"

Verbliebene Spalte in "Projekt-Nr" umbenennen und Projektnummern für die einzelnen Titel innerhalb des Projekts vergeben.

## Tabelle umsortieren
```
[
  {
    "op": "core/column-addition",
    "engineConfig": {
      "facets": [],
      "mode": "row-based"
    },
    "baseColumnName": "Projekt-Nr",
    "expression": "grel:\"\"",
    "onError": "set-to-blank",
    "newColumnName": "Notiz OA Team",
    "columnInsertIndex": 1,
    "description": "Create column Notiz OA Team at index 1 based on column Projekt-Nr using expression grel:\"\""
  },
  {
    "op": "core/column-addition",
    "engineConfig": {
      "facets": [],
      "mode": "row-based"
    },
    "baseColumnName": "Notiz OA Team",
    "expression": "grel:\"\"",
    "onError": "set-to-blank",
    "newColumnName": "Affiliation",
    "columnInsertIndex": 2,
    "description": "Create column Affiliation at index 2 based on column Notiz OA Team using expression grel:\"\""
  },
  {
    "op": "core/column-addition",
    "engineConfig": {
      "facets": [],
      "mode": "row-based"
    },
    "baseColumnName": "Affiliation",
    "expression": "grel:\"\"",
    "onError": "set-to-blank",
    "newColumnName": "URL",
    "columnInsertIndex": 3,
    "description": "Create column URL at index 3 based on column Affiliation using expression grel:\"\""
  },
  {
    "op": "core/column-rename",
    "oldColumnName": "Autor, Herausgeber oder Institution",
    "newColumnName": "Author",
    "description": "Rename column Autor, Herausgeber oder Institution to Author"
  },
  {
    "op": "core/column-rename",
    "oldColumnName": "Jahr ermittelt",
    "newColumnName": "Date",
    "description": "Rename column Jahr ermittelt to Date"
  },
  {
    "op": "core/column-rename",
    "oldColumnName": "Titel",
    "newColumnName": "Title",
    "description": "Rename column Titel to Title"
  },
  {
    "op": "core/column-rename",
    "oldColumnName": "Dokumententyp",
    "newColumnName": "Type",
    "description": "Rename column Dokumententyp to Type"
  },
  {
    "op": "core/column-rename",
    "oldColumnName": "Verlag",
    "newColumnName": "Publisher",
    "description": "Rename column Verlag to Publisher"
  },
  {
    "op": "core/column-rename",
    "oldColumnName": "Zeitschrift/Zeitung",
    "newColumnName": "Journal",
    "description": "Rename column Zeitschrift/Zeitung to Journal"
  },
  {
    "op": "core/column-rename",
    "oldColumnName": "Nummer",
    "newColumnName": "Issue",
    "description": "Rename column Nummer to Issue"
  },
  {
    "op": "core/column-reorder",
    "columnNames": [
      "Projekt-Nr",
      "Notiz OA Team",
      "Affiliation",
      "DOI",
      "URL",
      "Title",
      "Author",
      "Type",
      "Date",
      "Publisher",
      "Journal",
      "Issue",
      "ISSN",
      "eISSN",
      "ISBN"
    ],
    "description": "Reorder columns"
  }
]
```
## OAEZB
Das DFG-geförderte Projekt OA-EZB hatte zum Ziel, Angaben zu die besonderen Open-Access-Rechte von Allianz- und Nationallizenzen in der Elektronischen Zeitschriftenbibliothek (EZB) zu verankern und maschinell abfragbar zu machen. Resultat ist die OA-EZB-Schnittstelle, welche die in der EZB hinterlegten Open-Access-Rechte zur Veröffentlichung von Volltexten gemäß Allianz-, National- oder Konsortiallizenzen zur Verfügung stellt und für die keine Registrierung erforderlich ist. Um die OA-Berechtigung für die eigene Institution zu ermitteln, ist die EZB-Kennung anzugeben. Die Abfrage kann auf Artikel- oder Zeitschriftenebene erfolgen; Daten werden in den Formaten JSON oder XML zurückgegeben.

In einem ersten Schritt werden die Informationen im JSON Format bezogen.

### OAEZB über ISSN

* ISSN -> Edit column -> Add column by fetching URLs ... -> OAEZB_ISSN
* Wichtig: Throttle delay 1000 ms
* Wichtig: On Error: store error
```
'https://ezb.ur.de/api/oa_rights?bibid=ub...&issn=' + trim(cells.ISSN.value) + '&format=application/json'
```

* eISSN -> Edit column -> Add column by fetching URLs ... -> OAEZB_eISSN
* Wichtig: Throttle delay 1000 ms
* Wichtig: On Error: store error

```
'https://ezb.ur.de/api/oa_rights?bibid=ub...&issn=' + trim(cells.eISSN.value) + '&format=application/json'
```

Danach müssen die Spalten OAEZB_ISSN und OAEZB_eISSN fusioniert werden.

* OAEZB_ISSN → Edit column → Join columns

Beide Spalten auswählen.
Write result in new column named OAEZB
Delete joined columns

* OAEZB -> Edit column -> Add column based on this column -> OAEZB_STATUS

```
forEach(value.parseJson(), v,
  v.state + ' : ' + v.message
).uniques().join(' || ')
```

Anschließend wird eine neue Spalte OAEZB_RECHTSGRUNDLAGE angelegt, in der Werte aus verschiedenen OA-EZB-Feldern gespeichert werden:

Die Subfelder werden in der folgenden Reihenfolge und Form ausgegeben: "oa_agreement (EZB-Paket-ID: oa_package_name OA-Jg. oa_first_date - oa_last_date -> oa_pub_authority: oa_archivable_version (oa_source) in: oa_repositories (Embargo: oa_embargo_months)"
Gibt es mehrere Einträge, werden sie durch ----- getrennt.

In einem zweiten Schritt werden nicht benötigte Angaben gelöscht.

* OAEZB -> Edit column -> Add column based on this column -> OAEZB_RECHTSGRUNDLAGE
* Wichtig: Langauge Python/Jython

```
import json
oaezb = json.loads(value)
 
out = set([])
 
for i in oaezb:
    i_string = '{} (EZB-Paket-ID: {}) OA-Jg. {}-{} -> {}: {} ({}) in: {} (Embargo: {} Monate)'.format(
        i.get('oa_agreement', ''),
        i.get('oa_package_name', ''),
        i.get('oa_first_date', ''),
        i.get('oa_last_date', ''),
        i.get('oa_pub_authority', '').upper(),
        ' oder '.join([v for v in i.get('oa_archivable_version', {}).itervalues()]),
        ' oder '.join([v for v in i.get('oa_source', {}).itervalues()]),
        ' oder '.join([v for v in i.get('oa_repositories', {}).itervalues()]),
        i.get('oa_embargo_months', '')
    )
    out.add(i_string)
 
return ' ----- '.join(out)
```
Sind keine OA-Rechte hinterlegt, findet sich nun in der Spalte OAEZB_RECHTSGRUNDLAGE die folgende Angabe: ' (EZB-Paket-ID: ) OA-Jg. - -> : () in: (Embargo: Monate)'
Diese Angabe ist wenig hilfreich für die Rechteprüfung und wird daher gelöscht:

* OAEZB_RECHTSGRUNDLAGE -> Edit cells -> Transform...
```
if(value == ' (EZB-Paket-ID: ) OA-Jg. - -> :  () in:  (Embargo:  Monate)',
  null,
  value
)
```
Ggf. sind weitere Anmerkungen zu den OA-Rechten in der EZB hinterlegt; diese werden in einer neuen Spalte OAEZB_ANMERKUNGEN erfasst.
Dabei werden die Felder remarks_de und remarks_en ausgelesen; mehrere Einträge werden durch ----- getrennt.

* OAEZB -> Edit column -> Add column based on this column -> OAEZB_ANMERKUNGEN
```
Wichtig: Language: Python / Jython
import json
 
rem_out = set([])
for i in json.loads(value):
  rem_out.add(i.get('remarks_de'))
  rem_out.add(i.get('remarks_en'))
return ' ----- '.join([i.strip() for i in rem_out if i])
```

## Sherpa Romeo

Dokumentation API: https://v2.sherpa.ac.uk/api/

API-Call-URL hat folgendes Schema
 
`https://v2.sherpa.ac.uk/cgi/retrieve_by_id/cgi/retrieve_by_id?item-type=publication&api-key=UNSER_API_KEY&format=Json&identifier=ISSN`

Die Sherpa API gibt ein verschachteltes JSON-Ergebnis aus. Je nach Zeitschrift können unterschiedliche Wege für die OA-ZV vorgesehen sein. Die Ausgabe mit nur einem Weg (Beispiel Nomos) oder mit Pathway A und Pathway B (Beispiel Sage). Es gibt deshalb zwei Ermittlungen der möglichen Weg. Existiert nur ein Weg, erfolgt die Ausgabe in Pathway A.

### Sherpa Romeo Daten beziehen
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

### Pathway A

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

### Pathway B

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