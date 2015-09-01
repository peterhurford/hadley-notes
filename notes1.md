Various notes inspired from reading through [the last week of changes to ggplot](https://github.com/hadley/ggplot2/compare/87ea9df240d5254c4a583fec2c8291ce1c2b5847...81a00473f482c6287debcea01d22a089e10e2c96) by Hadley Wickham.

## dontrun vs. donttest

In Roxygen, `dontrun` will stop the provided examples from being run and evaluated by the documentation.

`donttest` will stop the provided examples from being run during tests (and CI) but will run them during documentation.

`dontrun` includes `donttest` and more.


## parse vs. as.name

```R
parse(text = "foo")
```

parse can be used to evaluate text:

```R
> x <- parse(text = "length(c(1, 1, 1))")
expression(length(c(1, 1, 1)))
> eval(x)
3

> x <- as.name("length(c(1, 1, 1))")
`length(c(1, 1, 1))`
> eval(x)
Error in eval(expr, envir, enclos) :
  object 'length(c(1, 1, 1))' not found
```

`as.name` produces a variable name that you can interact with.

```R
> grab_local <- function(nm) {
  eval(as.name(nm), envir = parent.frame())
}
> x <- 2
> grab_local("x")
2
```

The `as.name` makes this work:

```
> grab_local_bad <- function(nm) {
  eval(nm, envir = parent.frame())  
}
> grab_local_bad("x")
"x"
```

`as.name` is basically the opposite of `deparse(substitue())`.  `deparse(substitute())` turns a variable into a string, whereas `as.name()` turns a string into a variable.


## list vs. alist

`alist` is like `list`, expect it captures the promises.

> list(x = 1, y = x + 3)
Error: object 'x' not found
> alist(x = 1, y = x + 3)
$x
[1] 1

$y
x + 3

`...` passes any number of additional arguments into a function.

When in a function, `list(...)` will capture these additional arguments.

Anther way to capture these arguments is with `eval(substitue(alist(...)))`.

The differences are subtle:

```R
> capture <- function(...) { list(...) }
> capture2 <- function(...) { alist(...) }
> capture3 <- function(...) { eval(substitute(list(...))) }
> capture4 <- function(...) { eval(substitute(alist(...))) }
> capture(1, 2, 3)
[[1]]
[1] 1

[[2]]
[1] 2

[[3]]
[1] 3

> capture2(1, 2, 3)
[[1]]
...

> capture3(1, 2, 3)
[[1]]
[1] 1

[[2]]
[1] 2

[[3]]
[1] 3

> capture4(1, 2, 3)
[[1]]
[1] 1

[[2]]
[1] 2

[[3]]
[1] 3

> capture(1, c)
[[1]]
[1] 1

[[2]]
function (..., recursive = FALSE)  .Primitive("c")

> capture2(1, c)
[[1]]
...

> capture3(1, c)
[[1]]
[1] 1

[[2]]
function (..., recursive = FALSE)  .Primitive("c")

> capture4(1, c)
[[1]]
[1] 1

[[2]]
c
```

1.) `alist(...)` only returns `...` unless you wrap it in `eval(substitute())`.

2.) `list(...)` and `eval(substitue(list(...)))` are identical.

3.) `list(...)` will capture `c` as a name (like `substitute(c)`) whereas `eval(substitute(alist(...)))` will capture `c` as the function in the global environment (like `eval(substitute(c))`).
