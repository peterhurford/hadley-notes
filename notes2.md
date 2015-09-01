Various notes inspired from reading through [the last week of changes to ggplot](https://github.com/hadley/ggplot2/compare/87ea9df240d5254c4a583fec2c8291ce1c2b5847...81a00473f482c6287debcea01d22a089e10e2c96) by Hadley Wickham.


## Environments

* **Global environment** is the starting environment for R.

* Packages are attached (using `library`) to the global environment in a linked list.

* This creates a **search path** when looking for functions.  Starting from the global environment, it will look in the globalenv, then in the package most recently attached, then in the package second most recently attached, etc.

* If you're in an environment other than the globalenv, R will search that environment, and then all the parent environments, until it gets to the globalenv, and then the packages.

* Also see RobertZK's [super package](https://github.com/robertzk/super).


## :: vs. :::

* Functions in packages have to be exported, by editing `NAMESPACE` or using Roxygen2's `#' @export`.
* `::` gets functions that are explicitly exported for use.
* `:::` gets functions that are not exported, and are internal to the package.
* `:::` is analagous to `getNamespace(package)` where `package` is the package name.
* `::` is analagous to .


## Attaching vs. Loading

* **Attaching** puts a package on the search path.  **Loading** puts the package information into memory.  `library` involves both loading and attaching.


## Package Flow

Different R functions move packages from source, to installation, to memory:

![](http://r-pkgs.had.co.nz/diagrams/installation.png)

![](http://r-pkgs.had.co.nz/diagrams/loading.png)

("Build & Reload" is an RStudio feature.)


## ggproto

* Lightweight reference class, like R6.  Invented by Hadley for GGPlot.

* Why ggproto?  [Hadley explains](https://github.com/hadley/ggplot2/blob/master/vignettes/extending-ggplot2.Rmd).  ggplot2 originally used [proto](https://cran.r-project.org/web/packages/proto) because R6 and better solutions didn't exist yet.  When later modifications were needed, the options were to (1) adopt R6 (but it was a poor fit for ggplot) or (2) extend proto (but that required understanding proto and making sure changes didn't break other people's uses).  Since neither of these solutions were workable, it turned out creating a new OO system was the right call!

```R
object <- function() {
  env <- new.env(parent = parent.env(globalenv()))  
  env$self <- env
}
obj <- object()
obj$self
```
