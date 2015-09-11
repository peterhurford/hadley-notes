## Classes

The `class` of an object is just the class attribute of an object.  Attributes are meta-data for objects.

```R
> l <- list(1, 2, 3)
> class(l)
[1] "list"
> class(l) <- "foo"
> class(l)
[1] "foo"
> attr(l, "class") <- "boo"
> class(l)
[1] "boo"
```


## Pryr Package

[pryr](https://github.com/hadley/pryr) (pronounced "pry are") is a package created by Hadley to look at the internals of R.

#### `pryr::object_size`

Tells you the size of an object in memory.

```R
> pryr::object_size(seq(1e3))
4.04 kB
> pryr::object_size(seq(1e4))
40 kB
> pryr::object_size(seq(1e5))
400 kB
```

#### `pryr::where`

Tells you where a function is located.

```R
> library(pryr)
> where("where")
<environment: package:pryr>
attr(,"name")
[1] "package:pryr"
attr(,"path")
[1] "/Library/Frameworks/R.framework/Versions/3.2/Resources/library/pryr"
```

Improved upon by `batbelt::find_fun` (no need to pass a string and can find functions that are package prefixed).

```R
> batbelt::find_fun(where)
[1] "pryr"
> batbelt::find_fun(batbelt::find_fun)
[1] "batbelt"
```

It's easy to write `pryr::where` for yourself:

```R
where <- function(name) {
  env <- parent.frame()
  while(!exists(name, envir = env, inherits = FALSE)) {
    env <- parent.env(env)  
  }
  env
}
```


## R Memory

```R
> x <- list(1:1000000, 2:20000000)
> y <- x
> y[[2]][1] <- 3
> all.equal(x[[2]], y[[2]])
[1] "Mean relative difference: 0.5"
> all.equal(x[[2]][-1], y[[2]][-1])
[1] TRUE
> length(x[[2]])
[1] 19999999
> length(y[[2]])
[1] 19999999
> object_size(x)
84 MB
> object_size(y)
164 MB
```

**Mystery:** All we did was change one element, yet the memory size of the object doubled.  Why?

```R
> class(y[[2]])
[1] "numeric"
> class(x[[2]])
[1] "integer"
```

Not only was there a value change, but there was also a *class change*.  We went from numeric to integer, which requires double precision and thus double memory.

`all.equal` was fooled because it did type coercion when checking.  `identical` would have not been fooled.

> all.equal(x[[2]][-1], y[[2]][-1])
[1] TRUE
> identical(x[[2]][-1], y[[2]][-1])
[1] FALSE

We should have used `3L` instead of `3`.

```R
> class(3)
[1] "numeric"
> class(3L)
[1] "integer"
```

Let's try again...

```R
> x <- list(1:1000000, 2:20000000)
> y <- x
> y[[2]][1] <- 3L
> all.equal(x[[2]], y[[2]])
[1] "Mean relative difference: 0.5"
> all.equal(x[[2]][-1], y[[2]][-1])
[1] TRUE
> length(x[[2]])
[1] 19999999
> length(y[[2]])
[1] 19999999
> object_size(x)
84 MB
> object_size(y)
84 MB
```

Much better!



## `/` vs. `%/%`

`/` is regular (floating point) division, and `%/%` is floor (integer) division.

```R
> 3 / 4
[1] 0.75
> 3 %/% 4
[1] 0
```

`/` will even floating point divide integers.

```R
> 3L / 4L
[1] 0.75
```

The only way to preserve integer class is to divide an integer by an integer with `%/%`.

```R
> class(3 / 4)
[1] "numeric"
> class(3 %/% 4)
[1] "numeric"
> class(3L / 4L)
[1] "numeric"
> class(3L %/% 4L)
[1] "integer"
```


## inspect

`inspect` lets you look at memory addresses.  Let's use the `x` and `y` from the memory exercise:

```R
> inspect(x)
<VECSXP 0x7fd8f61d8790>
  <INTSXP 0x1057a8000>
  <INTSXP 0x118ca8000>
> inspect(y)
  <VECSXP 0x7fd8f61d87c8>
    <INTSXP 0x1057a8000>
    <REALSXP 0x14d7bb000>
```

Recall that y was a copy of x.  This allocated a new vector.  The first element of the copied vector is the same memory address as the first element of the original vector, so there is some efficiency in copying.

We can also see the type coercion that occured when replacing with `3` (instead of `3L`).
