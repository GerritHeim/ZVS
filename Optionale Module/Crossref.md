# Ergänzende Crossref Informationen

## Language
* CR -> Add column based on this column -> Language

`value.parseJson().message.language`

## PageFirst
* CR -> Add column based on this column -> PageFirst

`value.parseJson().message.page.split('-')[0]`

## PageLast
*  CR -> Add column based on this column -> PageLast

`value.parseJson().message.page.split('-')[1]`

## Heft/Artikel-Nr
Die Heft-Nr und die Artikel-Nr. werden ausgelesen und in zwei Spalten geschrieben.

* CR -> Add column based on this column -> Volume

`value.parseJson().message.volume`
* CR -> Add column based on this column -> Number

`value.parseJson().message['article-number']`

## Abstract
Abstract von CrossRef in das richtige Feld schreiben:

* Leere Spalte TitleAbstract anlegen
* TitleAbstract → Edit Cells → Transform

`cells.CR.value.parseJson().message.abstract`

Der von Crossref ermittelte Wert wird später überschrieben, wenn die Springer Nature-API ebenfalls ein Abstract liefert.

## Daten
Die Daten für Print und Online müssen für den OPUS Export aufgesplittet werden:

* DATE PUBLISHED PRINT → Edit column → Split into several columns

`by field lengths: 4, 4`

Anschließend die drei entstandenen Spalten "DATE PUBLISHED PRINT 1-2" umbenennnen in YEAR  PUBLISHED PRINT und MONTH-DAY PUBLISHED PRINT

* DATE PUBLISHED ONLINE → Edit column → Split into several columns

`by field lengths: 4, 4`

Anschließend die drei entstandenen Spalten "DATE PUBLISHED ONLINE 1-2" umbenennnen in YEAR PUBLISHED ONLINE und MONTH-DAY PUBLISHED ONLINE