## utils::write.csv

This function is weird:

```R
function (...) {
    Call <- match.call(expand.dots = TRUE)
    for (argname in c("append", "col.names", "sep", "dec", "qmethod")) if (!is.null(Call[[argname]])
)
        warning(gettextf("attempt to set '%s' ignored", argname),
            domain = NA)
    rn <- eval.parent(Call$row.names)
    Call$append <- NULL
    Call$col.names <- if (is.logical(rn) && !rn)
        TRUE
    else NA
    Call$sep <- ","
    Call$dec <- "."
    Call$qmethod <- "double"
    Call[[1L]] <- as.name("write.table")
    eval.parent(Call)
}
```

This function just takes dots and gets the function call used to call the package.

```R
> get_call_and_dots <- function(...) { match.call(expand.dots = TRUE) }
> get_call_and_dots(1, 2, 3)
get_call_and_dots(1, 2, 3)
> class(get_call_and_dots(1, 2, 3))
[1] "call"
```

It then modifies the call and rewrites the arguments using R black magic.

```R
> Call <- get_call_and_dots(1, 2, 3)
> Call
get_call_and_dots(1, 2, 3)
> Call[[2]] <- 4
> Call
get_call_and_dots(4, 2, 3)
```

It then ends by switching the function itself to `utils::write.table`, the more general file writing R function.

```R
> Call[[1]] <- as.name("utils::write.table")
> Call
`utils::write.table`(4, 2, 3)
```

-

Here's an alternative `write.csv`:

```R
write.csv2 <- function(...) {
  col_names <- if (is.logical(list(...)$row.names) && !is.logical(list(...)$row.names)) {
    TRUE
  } else { NA }
  utils::write.table(..., append = NULL, col.names = col_names,
    sep = ",", dec = ".", qmethod = "double")
}
```

Why they chose to rewrite the call rather than merely pass to another function is beyond me.
