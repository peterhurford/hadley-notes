## Bquote and `.`

* `bquote` will take an R expression and render it as-is.
* However, `.` within `bquote` will evaluate the R expression.

```R
add <- function(x, y) x + y
x <- 1
y <- 2
> bquote(add(x, y))
add(x, y)
> bquote(add(.(x), y))
add(1, y)
> bquote(add(x, .(y)))
add(x, 2)
> bquote(add(.(x), .(y)))
add(1, 2)
> bquote(.(add(x, y)))
[1] 3
```
