# Modul 2: Metadaten über Crossref

Es wird zunächst auf Basis der DOI der globale DOI-Resolver abgefragt, um zu überprüfen, ob die DOI syntaktisch korrekt ist und Metadaten bei Crossref registriert wurden. Eine Metadatenakquise kann nur für bei Crossref registrierte DOIs erfolgen. Daran schließt die Abfrage von Crossref an, um ein Grundset an Metadaten zu akquirieren. Dieses Grundset spalten die folgenden Schritte in unterschiedliche Spalten auf, um für die manuelle Prüfung leichtere Lesbarkeit zu ermöglichen.

## DOI-Registrar ermitteln
Über https://doi.org/doiRA/{DOI} lässt sich prüfen, ob eine DOI und wenn ja, von welcher DOI-Registry-Agency sie registriert wurde.

Crossref liefert nur Metadaten für von Crossref registrierte DOIs. Bleibt die spätere Crossref-Anfrage erfolglos, liefert die Anfrage an den DOI-Resolver Hinweise dazu, warum für eine DOI keine Metadaten von Crossref geliefert werden.

* DOI -> Edit column -> Add column based on this column -> doiRA
* Language: Python / Jython

```
import urllib2
import json
 
try:
    url = 'https://doi.org/doiRA/' + value.strip()
    r = urllib2.urlopen(url).read()
    j = json.loads(r)
except:
    return 'ERROR'
 
status = '||'.join(set([i.get('status', '') for i in j]))
if status:
    return status
 
ra = '||'.join(set([i.get('RA', '') for i in j]))
if ra:
    return ra
```

## Metadaten über Crossref vervollständigen
Vor Beginn ggf. überflüssige Leerzeichenn vor/nach den DOIs entfernen. Dazu DOI → Edit cells → Common transforms → Trim leading and trailing whitespace.
Angepasster Code für Abfrage:

* DOI -> Edit column -> Add column based on this column -> CR
* Language: Python / Jython

```
import urllib2
 
if cells.doiRA.value == 'Crossref':
    try:
        doi = cells.DOI.value.strip()
        url = 'https://api.crossref.org/works/' + doi + '?mailto=openaccess@ub.uni-frankfurt.de'
        response = urllib2.urlopen(url)
        return response.read().decode('unicode_escape')
    except:
        return None
else:
    return None
```

## GU Affiliation prüfen

> Der Code funktioniert so halbwegs. Er wirft brauchbare Affiliationen aus aber auch einige Fehler.

* Affiliation → Edit cells -Transform
* Language: Python / Jython

```
import json
 
# Affiliations aus Crossref
j = json.loads(cells.CR.value)
msg = j.get('message', {})
authors = msg.get('author', {})
aff_cr = []
for author in authors:
    for affiliation in author.get('affiliation', []):
        affili_name = affiliation.get('name', [])
        if 'Frankfurt' in affili_name:
            if 'Goethe' in affili_name or 'GU' in affili_name:
                aff_cr.append('{f}, {g}: {a}'.format(
                                                    f=author.get('family', ''),
                                                    g=author.get('given', ''),
                                                    a=affili_name))
 
# Rückgabe von beiden
return '{cr}'.format(
                                        cr='; '.join(aff_cr)
                                        )
```

## Publikationsdaten ermitteln
* CR -> Edit column -> Add column based on this column -> DATE PUBLISHED ONLINE

```
forEach(value.parseJson().message['published-online']['date-parts'][0],v,v).join('-')
```
* CR -> Edit column -> Add column based on this column -> DATE PUBLISHED PRINT

```
forEach(value.parseJson().message['published-print']['date-parts'][0],v,v).join('-')
```

## Autoren
Autoren aus CrossRef auslesen und Tabelleninhalt überschreiben, sofern was gefunden.

* Author > Edit cells -> Transform...
* Wichtig: On Error: keep original

```
forEach(cells['CR'].value.parseJson().message.author,v,
    v.family + ', ' + v.given
).join('; ')
```

## Titel
Titel aus CrossRef auslesen und Tabelleninhalt überschreiben, sofern was gefunden.

* Title -> Edit cells -> Transform...

```
forEach(cells.CR.value.parseJson().message.title,v,v).join('; ')
```

## Dokumententyp
Sofern es in den Crossref-Daten eine Angabe zum Dokumententyp gibt, wird diese übernommen, wobei drei Typen unserem Vokabular angepasst werden (z.B. "journal-article" -> "Article").

* Type -> Edit cells -> Transform...

```
if(isBlank(cells.CR.value.parseJson().message.type),
  value,
  if(cells.CR.value.parseJson().message.type == 'journal-article',
    'Article',
    if(cells.CR.value.parseJson().message.type == 'book-chapter',
      'Part of a Book',
      if(cells.CR.value.parseJson().message.type == 'proceedings-article',
        'Conference Object',
        cells.CR.value.parseJson().message.type
      )
    )
  )
)
```

## Verlag
* Publisher -> Edit cells -> Transform...

```
if(isBlank(cells.CR.value.parseJson().message.publisher),
  value,
  cells.CR.value.parseJson().message.publisher
)
```

## CR_LICENSE_URLs
* CR -> Edit column -> Add column based on this column -> CR_LICENSE_URLs
```
forEach(value.parseJson().message.license,v,
'content-version: ' + v['content-version'] + ' -- ' + v.URL
).join('; ')
```

## CR_ISSN_ALL
* CR -> Edit column -> Add column based on this column -> CR_ISSN_ALL
* Language: Python / Jython
```
import json
 
d = json.loads(cells.CR.value)
msg = d.get('message', [])
 
issn_all = set([])
 
for issn in msg.get('ISSN', []):
    issn_all.add(issn)
 
for issn in msg.get('issn-type', []):
    issn_all.add(issn.get('value'))
 
return '; '.join(issn_all)
```

## Trennung ISSN
* ISSN -> Edit cells -> Transform...

```
forEach(cells.CR.value.parseJson().message['issn-type'],
  v,
  if(v.type == 'print',
    v.value,
    null)
).join('; ')
```
+ eISSN -> Edit cells -> Transform...
```
forEach(cells.CR.value.parseJson().message['issn-type'],
  v,
  if(v.type == 'electronic',
    v.value,
    null)
).join('; ')
```
## ISBN -> Edit cells -> Transform...

* Language: Python / Jython
* import json
``` 
d = json.loads(cells.CR.value)
msg = d.get('message', [])
 
isbn_all = set([])
 
for isbn in msg.get('ISBN', []):
    isbn_all.add(isbn)
 
for isbn in msg.get('isbn-type', []):
    isbn_all.add(isbn.get('value'))
 
return '||'.join(isbn_all)
```
## CR_FUNDER
> Problematische Datenqualität bei Crossref. Ein Test ergab ziemlich viele zweifelhafte Daten.

* CR -> Edit column -> Add column based on this column -> CR_FUNDER
```
forEach(value.parseJson().message.funder, v,
    v.name + ' : ' + forEach(v.award, aw, aw).join(', ')
).join('; ')
```

## Verlagsort
* CR -> Add column based on this column -> PublisherPlace

```
value.parseJson().message['publisher-location']
```
## PersonEditor
* CR -> Add column based on this column -> PersonEditor

```
forEach(value.parseJson().message.editor,v,
  v.family + ', ' + v.given
).join('; ')
```
## DOI-Link
* DOI -> Edit column -> Add column based on this column -> DOI-Link
```
'https://doi.org/' + value
```