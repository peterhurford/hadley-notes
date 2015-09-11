## stringsAsFactors = FALSE

Data Frames require `stringsAsFactors = FALSE` to be passed in as a parameter, or else all strings in the dataframe will be converted to factors.  This is a common way R beginners are burned.

Why?  This is because R was optimized for modeling and modeling requires strings to be factors.  A good retrospective is ["stringsAsFactors: An unauthorized biography"](http://simplystatistics.org/2015/07/24/stringsasfactors-an-unauthorized-biography/).

One way to get around passing `stringsAsFactors = FALSE` to all your data.frames in your package is to redefine data.frame.  Hadley did this with `dplyr::data_frame`, which is a trimmed-down version of `data.frame` that never coerces inputs, never adds `row.names`, never munges column names, only recycles length 1 inputs, has lazy evaluation, and has `tbl_df` compatibility.


## exists with inherits = FALSE

`exists("name")` is used to determine whether `name` exists.  But if you don't do `exists("name", inherits = FALSE)` it will traverse the entire R namespace including the GlobalEnv, which could have unintended consequences within your package.


## Test Driven Development / Test Driven Refactoring

Tests are very important and should be the primary, if not only, way you use to check to see if your package is working.  When refactoring or adding new features, a good philosophy is test-driven-development (TDD) where you write new tests that define (or promise) the features that you will build.  These will start as red, but will become green as you add new features or refactor.

You can use existing tests to make sure your refactors don't break anything.
