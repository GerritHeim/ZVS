# Monografien ohne DOI

## Citavi Spalten umbenennen / sortieren
Umwandlung des Citavi Export in eine Arbeitstabelle mit den üblichen Bezeichnungen. Folgende Spalten sind enthalten:

* Projekt-Nr
* Author/Editor
* Title
* Subtitle
* Type
* Date
* Publisher
* Location
* ISBN
* DOI

```
[
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
    "oldColumnName": "Untertitel",
    "newColumnName": "Subtitle",
    "description": "Rename column Untertitel to Subtitle"
  },
  {
    "op": "core/column-rename",
    "oldColumnName": "Dokumententyp",
    "newColumnName": "Type",
    "description": "Rename column Dokumententyp to Type"
  },
  {
    "op": "core/column-removal",
    "columnName": "Auflage",
    "description": "Remove column Auflage"
  },
  {
    "op": "core/column-rename",
    "oldColumnName": "Verlag",
    "newColumnName": "Publisher",
    "description": "Rename column Verlag to Publisher"
  },
  {
    "op": "core/column-rename",
    "oldColumnName": "Ort",
    "newColumnName": "Location",
    "description": "Rename column Ort to Location"
  },
  {
    "op": "core/column-removal",
    "columnName": "Zeitschrift/Zeitung",
    "description": "Remove column Zeitschrift/Zeitung"
  },
  {
    "op": "core/column-removal",
    "columnName": "Seiten von–bis",
    "description": "Remove column Seiten von–bis"
  },
  {
    "op": "core/column-removal",
    "columnName": "Datum",
    "description": "Remove column Datum"
  },
  {
    "op": "core/column-removal",
    "columnName": "Nummer",
    "description": "Remove column Nummer"
  },
  {
    "op": "core/column-removal",
    "columnName": "ISSN",
    "description": "Remove column ISSN"
  },
  {
    "op": "core/column-removal",
    "columnName": "eISSN",
    "description": "Remove column eISSN"
  },
  {
    "op": "core/column-rename",
    "oldColumnName": "Autor, Herausgeber oder Institution",
    "newColumnName": "Author/Editor",
    "description": "Rename column Autor, Herausgeber oder Institution to Author/Editor"
  },
  {
    "op": "core/column-reorder",
    "columnNames": [
      "Projekt-Nr",
      "Author/Editor",
      "Title",
      "Subtitle",
      "Type",
      "Date",
      "Publisher",
      "Location",
      "ISBN",
      "DOI"
    ],
    "description": "Reorder columns"
  }
]
```