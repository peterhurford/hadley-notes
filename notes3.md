Various notes inspired from reading through [the last week of changes to ggplot](https://github.com/hadley/ggplot2/compare/87ea9df240d5254c4a583fec2c8291ce1c2b5847...81a00473f482c6287debcea01d22a089e10e2c96) by Hadley Wickham.

## body

* `body(fn)` gives the body of a function.
* This can be interpreted as a list of expressions.
* `class(body(fn))` is `{`.  That's weird.
* `{` is also a function.

```R
> quote(`{`(y <- 1, a + b))
{
      y <- 1
      a + b
}
```

* `body` and `formals` can be used to write a function directly.

```R
> foo <- function () {}
> foo
function () {}
> body(foo) <- quote(`{`(y <- 1, y + z))
> foo
function ()
{
      y <- 1
      y + z
}
> formals(foo) <- alist(z=)
> foo
function (z)
{
      y <- 1
      y + z
}
```


## ggplot2:::find_names vs. all.vars

find_names <- function(expr) {
  if (is.call(expr)) {
    unlist(lapply(expr[-1], find_names))
  } else if (is.name(expr)) {
      +    as.character(expr)
      +  }
      +}

They seem to be the same?

Hadley has been known to rewrite functions to avoid `.` in variable names.


## setdiff

* You probably know this.

* Cool thing -- setdiff preserves order.

```R
> setdiff(c("a", "b", "c"), c("d", "e", "c"))
[1] "a" "b"
> setdiff(c("c", "b", "a"), c("d", "e", "c"))
[1] "b" "a"
```

* Also useful:

```R
# Get everything that isn't shared by the two sets.
symmetric_diff <- function(a, b) {
  union(setdiff(a, b), setdiff(b, a))
}
```


## Searching for function families in base R.

* `builtins()` is the list of all base functions.
* `grep("set", builtins(), value = TRUE)` gives you the names of functions that contain the word "set"


## Multi-argument functions

```R
> union(c(1, 2), c(3, 4))
[1] 1 2 3 4
> union(c(1, 2), c(3, 4), c(5, 6))
Error in union(c(1, 2), c(3, 4), c(5, 6)) : unused argument (c(5, 6))
> Reduce(union, list(c(1, 2), c(3, 4), c(5, 6)))
[1] 1 2 3 4 5 6
> multiunion <- function(...) {
    Reduce(union, list(...))
  }
> multiunion(c(1, 2), c(3, 4), c(5, 6))
[1] 1 2 3 4 5 6
```


## NROW vs. nrow

* `NROW` works on both vectors and data.frames, whereas `nrow` works only on data.frames.
```R
> NROW(iris)
[1] 150
> nrow(iris)
[1] 150
> NROW(c(1, 2, 3))
[1] 3
> nrow(c(1, 2, 3))
NULL
```


## message_wrap utility

* [Useful utility](https://github.com/hadley/ggplot2/blob/master/R/utilities.r#L264-L274).  [Example of use](https://github.com/hadley/ggplot2/blob/master/R/scales-.r#L26-L29).


## Roxygen include

* Collates your files in the correct order, so you can include variables from previous files in the correct order.
* [Example from stagerunner](https://github.com/robertzk/stagerunner/blob/master/R/stagerunner.R#L1-L5).
