## match

* `match(x, y)` tells you the positions of x within y.
* `x %in% y` is a logical that tells you that `match(x, y)` has length > 0.
* `%in%` actually calls match internally

```R
> match(c(2, 3), seq(2, 10))
[1] 1 2
> c(2, 3) %in% seq(2, 10)
[1] TRUE TRUE
## no matches results in NA
> c(2, 11) %in% seq(2, 10)
[1]  TRUE FALSE
> match(c(2, 11), seq(2, 10))
[1]  1 NA
```

**Question? Is it `match` equivalent to `which(x %in% y)`**

```R
> match(c(2, 11), seq(2, 10))
[1]  1 NA
> which(c(2, 11) %in% seq(2, 10))
[1] 1
> which(c(4, 5) %in% seq(2, 10))
[1] 1 2
> match(c(4, 5), seq(2, 10))
[1] 3 4
```

Nope, `match` is different, since it will get you the actual positions.  `which` will just render the logicals, which is not what you want in this case.
