## Devtools!

* Devtools is Hadley's second most important package, as judged by star count.
* Most people know it for `install_github`, but it actually does a ton of developer things:
    * `document` is actually a devtools function that *calls out* to Roxygen.
    * `test` is actually a devtools function that *calls out* to testthat.
    * Devtools covers installation, testing, and documentation.


## How packages work

In the days before Devtools, packages were installed directly from CRAN through `install.packages` or you downloaded a binary file and installed it manually.

`devtools::install_github` was a revolution because it meant that R packages could be hosted effortlessly on GitHub and installed effortlessly in R.  This broke the CRAN cartel.

Also, all CRAN packages are unofficially mirrored on https://github.com/cran so it's easy to use GitHub for all your R developing needs.  I don't recommend installing from https://github.com/cran but it is useful to effortlessly read source code of CRAN packages.


## Bioconductor

Looking over [the latest Devtools changes](https://github.com/hadley/devtools/compare/eb1a7721194fc4f272443d32ae4e7ec7763e7e62...12b6c391cd357c244cfd6830a4aec76b67a4635c), we saw that Hadley worked on improving installation from Bioconductor.

[Bioconductor](https://www.bioconductor.org/) is a CRAN-like repository for genomics-related R pacakges.


## Big news: repos in Descriptions

Hadley [is making it easier to manage dependencies from GitHub repos](https://github.com/hadley/devtools/blob/master/vignettes/dependencies.Rmd) by allowing you to include Remotes in the `DESCRIPTION` file.  This is a problem we had previously used [lockbox](https://github.com/robertzk/lockbox) to solve.

## lapply

People know what `lapply` does, but a feature Hadley was using that was useful was passing in arguments, since `lapply` takes a `...` that is passed to the mapping function.

In this example, the two calls of `lapply` are equivalent because `lapply` can pass named arguments:

```R
trims <- c(0, 0.1, 0.2, 0.5)
x <- rcauchy(100)
lapply(trims, function(trim) mean(x, trim = trim))
lapply(trims, mean, x = x)
```

## mapply and Map

We also reviewed `mapply` which can be used to loop over multiple arguments:

```R
> mapply(function(x, y) x + y, seq(1, 3), seq(2, 4))
[1] 3 5 7
> mapply(function(x, y, z) x + y + z, seq(1, 3), seq(2, 4), seq(3, 5))
[1] 6 9 12
```

A preferred version of this is `Map`, which keeps thins in list form by calling `mapply` with `SIMPLIFY = FALSE`.

```R
> Map(function(x, y, z) x + y + z, seq(1, 3), seq(2, 4), seq(3, 5))
[[1]]
[1] 6

[[2]]
[1] 9

[[3]]
[1] 12
```


## Roxygen's importFrom

Roxygen has an `importFrom` which allows you to import a particular function from a package for use in your function.  This is useful because it lets you avoid needing to use `::` and the package name.  However, it should be used sparingly and [even the author suggests not using it](https://github.com/klutometis/roxygen/blob/cbd488066b7def0de00d679a076fbcaca94b74a0/vignettes/namespace.Rmd).  Currently, I just use it for magrittr.

Also, apparently this `importFrom` still will import the entire package to get the function, unlike Python's import from.
