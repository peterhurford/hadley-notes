## utils::type.convert

Performs a bunch of type conversions based on the input you pass.

Convert character to factor:

```R
> type.convert(c("AK", "AL"))
[1] AK AL
Levels: AK AL
```

Convert character to number:

```R
> type.convert(c("1", "2", "3"))
[1] 1 2 3
```

Convert character to logical:

```R
> type.convert(c("FALSE", "TRUE", "T", "F"))
[1] FALSE  TRUE  TRUE FALSE
```

`as.is` will coerce to number or logical, but not factor:

```R
> type.convert(c("AK", "1", "FALSE"), as.is = TRUE)
[1] "AK"    "1"     "FALSE"
> type.convert(c("1", "2", "3"), as.is = TRUE)
[1] 1 2 3
> type.convert(c("TRUE", "FALSE"), as.is = TRUE)
[1]  TRUE FALSE
> type.convert(c("AK", "AL"), as.is = TRUE)
[1] "AK" "AL"
```
