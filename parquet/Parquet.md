Parquet
=======

-	[parquet mirror](https://github.com/apache/parquet-mr)
-	[parquet format mirror](https://github.com/apache/parquet-format)

Hierarchically, a file consists of one or more row groups. A row group contains exactly one column chunk per column. Column chunks contain one or more pages.

```
+----------------------------------+
| Row Group                        |
| +--------+ +--------+ +--------+ |
| | Column | | Column | | Column | |
| | Chunk  | | Chunk  | | Chunk  | |
| | +----+ | | +----+ | | +----+ | |
| | |page| | | |page| | | |page| | |
| | +----+ | | +----+ | | +----+ | |
| | |page| | |        | | |page| | |
| | +----+ | |        | | +----+ | |
| +--------+ +--------+ +--------+ |
+----------------------------------+
```

**note:** `page` is conceptually an indivisible unit
