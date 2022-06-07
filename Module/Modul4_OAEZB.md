# OAEZB
Das DFG-geförderte Projekt OA-EZB hatte zum Ziel, Angaben zu die besonderen Open-Access-Rechte von Allianz- und Nationallizenzen in der Elektronischen Zeitschriftenbibliothek (EZB) zu verankern und maschinell abfragbar zu machen. Resultat ist die OA-EZB-Schnittstelle, welche die in der EZB hinterlegten Open-Access-Rechte zur Veröffentlichung von Volltexten gemäß Allianz-, National- oder Konsortiallizenzen zur Verfügung stellt und für die keine Registrierung erforderlich ist. Um die OA-Berechtigung für die eigene Institution zu ermitteln, ist die EZB-Kennung anzugeben. Die Abfrage kann auf Artikel- oder Zeitschriftenebene erfolgen; Daten werden in den Formaten JSON oder XML zurückgegeben.

In einem ersten Schritt werden die Informationen im JSON Format bezogen.

## OA EZB über DOI

Die Anfrage kann auf Basis der DOI oder der ISSN erfolgen:

* DOI -> Edit column -> Add column by fetching URLs ... -> OAEZB
* Wichtig: Throttle delay 1000 ms
* Wichtig: On Error: store error

````
'https://ezb.ur.de/api/oa_rights?bibid=ub...&doi=' + trim(cells.DOI.value) + '&format=application/json'
````

**ODER**

## OA EZB über ISSN

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

Unabhängig, ob die Abfrage via DOI oder ISSN/eISSN erfolgte, ist die folgende Bearbeitung des Ergebnisses notwendig. Um die JSON Ausgabe in ein leichter lesbares Format umzuwandeln ist folgender Schritt notwendig.

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