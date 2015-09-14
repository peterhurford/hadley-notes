## Dplyr

#### History

Hadley [had a thesis about how to properly structure data](http://had.co.nz/thesis/), based on [third-normal form databases](https://en.wikipedia.org/wiki/Third_normal_form) (see [Edgar Codd](https://en.wikipedia.org/wiki/Edgar_F._Codd)).  This thesis resulted in the [reshape package](https://github.com/hadley/reshape) to turn data into the proper form.

[Plyr](https://github.com/hadley/plyr) was then built on reshape to provide ways of manipulating data within this framework.  Dplyr was built on plyr to provide a larger amount of possible manipulations, but restricted just to data frames.

Dplyr also lets you work out-of-memory from an external database, like Postgres.


#### Learning Dplyr

```R
lahman_df() %>%                     # Take the dataframe
  tbl("Batting") %>%                # Grab the "Batting" table
  group_by(yearID) %>%              # Group by year -- only matters under the hood
  do(mod = lm(R ~ AB, data = .))    # Fit a model to each group (`do` is like `apply` from Pandas)
```

See also:
* ["Do your data janitor work like a boss with dplyr"](http://www.r-bloggers.com/do-your-data-janitor-work-like-a-boss-with-dplyr/)
* ["Dplyr Tutorial"](http://genomicsclass.github.io/book/pages/dplyr_tutorial.html)


#### Reading from Postgres in Dplyr

Connect to a Postgres via `src_postgres` and get a databse object.  You can then take the database and call `tbl` with the database and a table name to get a particular table from the database.

Dplyr relies on RPostgres.  But the advantage to reading from the database using dplyr is that dplyr can maintain a lazy connection to the database where the database information is not forced into memory except when you need it or specifically require it.

To write to postgres via Dplyr, just use RPostgres.


#### Options OutDec

This option controls the decimal separator.  Apparently it is changable.
```R
> 1000.222
[1] 1000.222
> options("OutDec")
$OutDec
[1] "."
> options(OutDec = ",")
> 1000.222
[1] 1000,222
```

But it still makes you type `.` in the REPL...

```R
> 1000,222
Error: unexpected ',' in "1000,"
```

You can also go a little crazy:

```R
> options(OutDec = "blah")
> 1000.222
[1] 1000blah222
> options(OutDec = "")
> 1000.222
[1] 1000222
```


#### `is.name` vs. `is.symbol`

They're identical and both determine whether something is a symbol.
