# ZVS @ UB JCS
Module für den Zweitveröffentlichungsservice Workflow mit OpenRefine, der an der UB J.C. Senckenberg in Frankfurt im Einsatz ist. (UB JCS)

 1. Citavi
 1. Metadaten über Crossref
 1. Open Access Status
 1. OAEZB
 1. Sherpa Romeo
 
Optionale Module für Metadatenakquise vor Import:
1. arXiv
1. CORE
1. Crossref
1. Pubmed
1. Springer
 
Bei Automatisierung (JSON Export nach erfolgreichem Durchlauf) beachten, dass die Module in der gleichen Reihenfolge durchlaufen werden müssen, da OpenRefine vorherige Änderungen voraussetzt. Die Nummierung ist eine Empfehlung, die sich in der Praxis bewährt hat.

Zusätzlich ein theoretisches Konzept für einen Massenimport aus OpenRefine in OPUS (nicht getestet)

## Anmerkungen
Der vorliegende Workflow basiert auf Anpassung und Nachnutzung von [tuub/oagreenservice](https://github.com/tuub/oagreenservice) von [Michaela Voigt](https://github.com/michaelavoigt)  und [Sebastian Dittmann](https://github.com/sebDit). Änderungen betreffen vor allem die Unterschiede bei einem OPUS-Zielrepositorium und teilweise andere Anforderungen in Frankfurt.
