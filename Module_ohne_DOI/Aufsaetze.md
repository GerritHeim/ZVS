# Aufsätze ohne DOI

## Citavi Spalten umbenennen / sortieren
Umwandlung des Citavi Export in eine Arbeitstabelle mit den üblichen Bezeichnungen. Folgende Spalten sind enthalten:

* Projekt-Nr.
* Author
* Title
* Subtitle
* Parent
* Pages
* Type
* Date

```
[
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
    "oldColumnName": "Untertitel",
    "newColumnName": "Subtitle",
    "description": "Rename column Untertitel to Subtitle"
  },
  {
    "op": "core/column-rename",
    "oldColumnName": "Übergeordneter Titeleintrag (In:)",
    "newColumnName": "Parent",
    "description": "Rename column Übergeordneter Titeleintrag (In:) to Parent"
  },
  {
    "op": "core/column-rename",
    "oldColumnName": "Dokumententyp",
    "newColumnName": "Type",
    "description": "Rename column Dokumententyp to Type"
  },
  {
    "op": "core/column-rename",
    "oldColumnName": "Seiten von–bis",
    "newColumnName": "Pages",
    "description": "Rename column Seiten von–bis to Pages"
  },
  {
    "op": "core/column-reorder",
    "columnNames": [
      "Projekt-Nr",
      "Author",
      "Title",
      "Subtitle",
      "Parent",
      "Pages",
      "Type",
      "Date"
    ],
    "description": "Reorder columns"
  }
]
```