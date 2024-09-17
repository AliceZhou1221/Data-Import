data_import
================
2024-09-17

This document will show how to import data

\##improt the FAS Litters CSV

``` r
#relative path to the github.io
getwd()
```

    ## [1] "/Users/alicezhou/Desktop/DS1/Data Import"

``` r
litters_df = read_csv(file = "data_import_examples/FAS_litters.csv")
```

    ## Rows: 49 Columns: 8
    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: ","
    ## chr (4): Group, Litter Number, GD0 weight, GD18 weight
    ## dbl (4): GD of Birth, Pups born alive, Pups dead @ birth, Pups survive
    ## 
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

What’s missing in the data

``` r
litters_df =
  read_csv(
    file = "data_import_examples/FAS_litters.csv",
    na = c("NA", "", "."),
    col_types = cols(
      Group = col_factor()
    )
  )

litters_df = janitor::clean_names(litters_df)
pull(litters_df, gd0_weight)
```

    ##  [1] 19.7 27.0 26.0 28.5   NA   NA   NA   NA   NA 28.5 28.0   NA   NA   NA   NA
    ## [16] 17.0 21.4   NA   NA   NA 28.0 23.5 22.6   NA 21.7 24.4 19.5 24.3 22.6 22.2
    ## [31] 23.8 22.6 23.8 25.5 23.9 24.5   NA   NA 26.9 27.5 28.5 33.4 21.8 25.4 20.0
    ## [46] 21.8 25.6 23.5 25.5
