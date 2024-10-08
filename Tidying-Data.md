Tidying Data
================
2024-09-24

``` r
library(tidyverse)
```

    ## ── Attaching core tidyverse packages ──────────────────────── tidyverse 2.0.0 ──
    ## ✔ dplyr     1.1.4     ✔ readr     2.1.5
    ## ✔ forcats   1.0.0     ✔ stringr   1.5.1
    ## ✔ ggplot2   3.5.1     ✔ tibble    3.2.1
    ## ✔ lubridate 1.9.3     ✔ tidyr     1.3.1
    ## ✔ purrr     1.0.2     
    ## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
    ## ✖ dplyr::filter() masks stats::filter()
    ## ✖ dplyr::lag()    masks stats::lag()
    ## ℹ Use the conflicted package (<http://conflicted.r-lib.org/>) to force all conflicts to become errors

``` r
library(readxl)
library(haven)
options(tiblle.print_min = 5)
```

``` r
pulse_df = 
  haven::read_sas("./data/public_pulse_data.sas7bdat") %>% 
  janitor::clean_names()
pulse_df
```

    ## # A tibble: 1,087 × 7
    ##       id   age sex    bdi_score_bl bdi_score_01m bdi_score_06m bdi_score_12m
    ##    <dbl> <dbl> <chr>         <dbl>         <dbl>         <dbl>         <dbl>
    ##  1 10003  48.0 male              7             1             2             0
    ##  2 10015  72.5 male              6            NA            NA            NA
    ##  3 10022  58.5 male             14             3             8            NA
    ##  4 10026  72.7 male             20             6            18            16
    ##  5 10035  60.4 male              4             0             1             2
    ##  6 10050  84.7 male              2            10            12             8
    ##  7 10078  31.3 male              4             0            NA            NA
    ##  8 10088  56.9 male              5            NA             0             2
    ##  9 10091  76.0 male              0             3             4             0
    ## 10 10092  74.2 female           10             2            11             6
    ## # ℹ 1,077 more rows

this need to go from wide to long format \##pivot longer

``` r
pulse_tidy_df = 
  pulse_df %>% 
  pivot_longer(
    cols = bdi_score_bl:bdi_score_12m,
    names_to = "visit",
    values_to = "bdi_score",
    names_prefix = "bdi_score_"
  ) %>% 
  mutate(
    visit = replace(visit, visit == "bl", "00m")
  ) %>% 
  relocate(id, visit)
pulse_tidy_df
```

    ## # A tibble: 4,348 × 5
    ##       id visit   age sex   bdi_score
    ##    <dbl> <chr> <dbl> <chr>     <dbl>
    ##  1 10003 00m    48.0 male          7
    ##  2 10003 01m    48.0 male          1
    ##  3 10003 06m    48.0 male          2
    ##  4 10003 12m    48.0 male          0
    ##  5 10015 00m    72.5 male          6
    ##  6 10015 01m    72.5 male         NA
    ##  7 10015 06m    72.5 male         NA
    ##  8 10015 12m    72.5 male         NA
    ##  9 10022 00m    58.5 male         14
    ## 10 10022 01m    58.5 male          3
    ## # ℹ 4,338 more rows

one more example!

``` r
litters_df = 
  read_csv("./data/FAS_litters.csv", na = c("NA", ".","")) %>% 
  janitor::clean_names() %>% 
  pivot_longer(
    cols = gd0_weight:gd18_weight,
    names_to = "gd_time",
    values_to = "weight"
  )
```

    ## Rows: 49 Columns: 8
    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: ","
    ## chr (2): Group, Litter Number
    ## dbl (6): GD0 weight, GD18 weight, GD of Birth, Pups born alive, Pups dead @ ...
    ## 
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

``` r
pulse_df = 
  haven::read_sas("./data/public_pulse_data.sas7bdat") |>
  janitor::clean_names() |>
  pivot_longer(
    bdi_score_bl:bdi_score_12m,
    names_to = "visit", 
    names_prefix = "bdi_score_",
    values_to = "bdi") |>
  mutate(
    visit = replace(visit, visit == "bl", "00m"),
    visit = factor(visit)) 

print(pulse_df, n = 12)
```

    ## # A tibble: 4,348 × 5
    ##       id   age sex   visit   bdi
    ##    <dbl> <dbl> <chr> <fct> <dbl>
    ##  1 10003  48.0 male  00m       7
    ##  2 10003  48.0 male  01m       1
    ##  3 10003  48.0 male  06m       2
    ##  4 10003  48.0 male  12m       0
    ##  5 10015  72.5 male  00m       6
    ##  6 10015  72.5 male  01m      NA
    ##  7 10015  72.5 male  06m      NA
    ##  8 10015  72.5 male  12m      NA
    ##  9 10022  58.5 male  00m      14
    ## 10 10022  58.5 male  01m       3
    ## 11 10022  58.5 male  06m       8
    ## 12 10022  58.5 male  12m      NA
    ## # ℹ 4,336 more rows

LA

``` r
litters_clean_df = 
  read_csv("./data/FAS_litters.csv") %>% 
  janitor::clean_names() %>% 
  select(litter_number, gd0_weight, gd18_weight) %>% 
  pivot_longer(
    cols = gd0_weight: gd18_weight,
    names_to = "gd", 
    names_prefix = "gd_weight",
    values_to = "weight") %>% 
  mutate(
    gd = case_match(
      gd,
      "gd0_weight"  ~ 0,
      "gd18_weight" ~ 18
    )
  )
```

    ## Rows: 49 Columns: 8
    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: ","
    ## chr (4): Group, Litter Number, GD0 weight, GD18 weight
    ## dbl (4): GD of Birth, Pups born alive, Pups dead @ birth, Pups survive
    ## 
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

\##pivot wider

``` r
analysis_df = 
  tibble(
    group = c("treatment", "treatment", "control", "control"),
    time = c("pre", "post","pre", "post"),
    mean = c(4, 10, 4.2, 5)
  )
```

pivot wider for human readability

``` r
analysis_df %>% 
  pivot_wider(
    names_from = time,
    values_from = mean
  )
```

    ## # A tibble: 2 × 3
    ##   group       pre  post
    ##   <chr>     <dbl> <dbl>
    ## 1 treatment   4      10
    ## 2 control     4.2     5
