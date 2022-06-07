# Modul 1: Literaturliste mit Citavi normalisieren
Import in Citavi-Projekt (über Zwischenablage mit DOI, da Excelimport trotz mehrfacher Versuche momentan irgendwie fehlerhaft ist)

## Erste Anreicherung über Citavi DOI Suche

(Warnung) Der DOI-basierte Import in Citavi ist bei anderen Dokumenttypen als ZS-Artikeln mit Vorsicht zu genießen:

* Probleme: Citavi setzt für Sammelbandbeiträge die Aufnahme des gesamten Bandes in die Liste voraussetzt, um dann von dort darauf zu verweisen. Die Gesamtband-Einträge sollten dann nach dem Export wieder aus der Citavi-Exporttabelle gelöscht werden - nur für die Einzelbeiträge wird eine Rechteprüfung vorgenommen.
* Problem: Sammelbandbeiträge ohne eigene DOI und DOI nur auf den gesamten Band verweist. Hier kann es vorkommen, dass Citavi nur die Metadaten des Bandes importiert bzw. die bibliographischen Angaben des Beitrags mit denen des Bandes überschreibt.
* Lösung: Lässt sich ganz gut durch einen Abgleich der Ausgangsliste mit Citavi erreichen, wenn aus der Ausgangsliste Dokumenttypen hervorgehen.

## Citavi-Export für OpenRefine vorbereiten
1. Tabellenansicht öffnen mit STRG + ALT + T → Export in Excel. Hier zusätzlich beim ersten Mal über Ansicht → Spalten die Spalten für ISSN und eISSN einblenden. Ggf. auch Spalte für das Feld, in dem nicht benötigte Sammelwerke markiert wurden, einsetzen.
2. Anschließend in Excel die ersten 2 Spalten löschen, weil OpenRefine sich an diesen "verschluckt"
3. Verbliebene Spalte in "Projekt-Nr" umbenennen und Projektnummern für die einzelnen Titel innerhalb des Projekts vergeben.

## Citavi-Tabelle mittels OpenRefine umformatieren
Um die Citavi Tabelle in das Format zu bringen, das der Workflow beim Start erwartet muss diese in OpenRefine eingelesen werden und die Spalten umbenannt und in eine andere Reihenfolge gebracht werden:

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