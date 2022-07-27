# Open Access Status (OpenAlex)
Die OA Informationen werden von OpenAlex bezogen. Der Vorteil ggü. dem direkten Bezug über Unpaywall ist die leichter verständliche Darstellung der Informationen.

> Daten werden im JSON-Format ausgegeben.

* DOI → Edit Column → Add Column by fetching URLs .... → OpenAlex
* Wichtig: Throttle delay 1000 ms
* Wichtig: On Error: store error

```
'https://api.openalex.org/works/doi:' + value
```

Als Ergebnis werden die in OpenAlex verfügbaren Informationen als JSON String ausgegeben und aus diesem String können nun die entsprechenden Bestandteile herausgefiltert werden.

## OA-Status

OpenAlex → Edit Column → Add Column based on this Column → OA_Status

```
value.parseJson().open_access.is_oa
```

## OA-Farbe

OpenAlex → Edit Column → Add Column based on this Column → OA_Color

```
value.parseJson().open_access.oa_status
```

## OA-Repos

OpenAlex Edit Column → Add Column based on this Column → OA_Repo
```
value.parseJson().open_access.oa_url
```

## Lizenz

OpenAlex → Edit Column → Add Column based on this Column → Licence
```
value.parseJson().host_venue.license
```