# arXiv

* DOI -> edit column -> Add column by fetching URLs -> ARXIV

`'http://export.arxiv.org/api/query?search_query=doi:' + value`

* ARXIV -> Edit column -> Add column based on this column -> ARXIV_abstract

```
forEach(value.parseHtml().select('summary'),v,
    v.htmlText()
).uniques().join('; ')
```