data manipulation with ‘dplyr’
================
2022-09-22

``` r
library(tidyverse)
```

    ## ── Attaching packages ─────────────────────────────────────── tidyverse 1.3.2 ──
    ## ✔ ggplot2 3.3.6      ✔ purrr   0.3.4 
    ## ✔ tibble  3.1.8      ✔ dplyr   1.0.10
    ## ✔ tidyr   1.2.0      ✔ stringr 1.4.1 
    ## ✔ readr   2.1.2      ✔ forcats 0.5.2 
    ## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
    ## ✖ dplyr::filter() masks stats::filter()
    ## ✖ dplyr::lag()    masks stats::lag()

``` r
options(tibble.print_min = 3)

litters_data = read_csv("./data/FAS_litters.csv",
  col_types = "ccddiiii")
litters_data = janitor::clean_names(litters_data)

pups_data = read_csv("./data/FAS_pups.csv",
  col_types = "ciiiii")
pups_data = janitor::clean_names(pups_data)
```

## select

can select a subset of data, specify which columns you want to keep

``` r
select(litters_data, group, litter_number, gd0_weight, pups_born_alive)
```

    ## # A tibble: 49 × 4
    ##   group litter_number gd0_weight pups_born_alive
    ##   <chr> <chr>              <dbl>           <int>
    ## 1 Con7  #85                 19.7               3
    ## 2 Con7  #1/2/95/2           27                 8
    ## 3 Con7  #5/5/3/83/3-3       26                 6
    ## # … with 46 more rows

select a range of columns to include:

``` r
select(litters_data, group:gd_of_birth)
```

    ## # A tibble: 49 × 5
    ##   group litter_number gd0_weight gd18_weight gd_of_birth
    ##   <chr> <chr>              <dbl>       <dbl>       <int>
    ## 1 Con7  #85                 19.7        34.7          20
    ## 2 Con7  #1/2/95/2           27          42            19
    ## 3 Con7  #5/5/3/83/3-3       26          41.4          19
    ## # … with 46 more rows

specify R which column you don’t want to include, can delete multiple
variables in one line:

``` r
select(litters_data, -pups_survive)
```

    ## # A tibble: 49 × 7
    ##   group litter_number gd0_weight gd18_weight gd_of_birth pups_born_alive pups_…¹
    ##   <chr> <chr>              <dbl>       <dbl>       <int>           <int>   <int>
    ## 1 Con7  #85                 19.7        34.7          20               3       4
    ## 2 Con7  #1/2/95/2           27          42            19               8       0
    ## 3 Con7  #5/5/3/83/3-3       26          41.4          19               6       0
    ## # … with 46 more rows, and abbreviated variable name ¹​pups_dead_birth

rename the variable:

``` r
select(litters_data, GROUP = group, LiTtEr_NuMbEr = litter_number)
```

    ## # A tibble: 49 × 2
    ##   GROUP LiTtEr_NuMbEr
    ##   <chr> <chr>        
    ## 1 Con7  #85          
    ## 2 Con7  #1/2/95/2    
    ## 3 Con7  #5/5/3/83/3-3
    ## # … with 46 more rows

also can use “rename” feature:

``` r
rename(litters_data, GROUP = group, LiTtEr_NuMbEr = litter_number)
```

    ## # A tibble: 49 × 8
    ##   GROUP LiTtEr_NuMbEr gd0_weight gd18_weight gd_of_birth pups_…¹ pups_…² pups_…³
    ##   <chr> <chr>              <dbl>       <dbl>       <int>   <int>   <int>   <int>
    ## 1 Con7  #85                 19.7        34.7          20       3       4       3
    ## 2 Con7  #1/2/95/2           27          42            19       8       0       7
    ## 3 Con7  #5/5/3/83/3-3       26          41.4          19       6       0       5
    ## # … with 46 more rows, and abbreviated variable names ¹​pups_born_alive,
    ## #   ²​pups_dead_birth, ³​pups_survive

if variables are named with suffixes or other standard patterns and want
to select those variables:

``` r
select(litters_data, starts_with("gd"), ends_with("weight"))
```

    ## # A tibble: 49 × 3
    ##   gd0_weight gd18_weight gd_of_birth
    ##        <dbl>       <dbl>       <int>
    ## 1       19.7        34.7          20
    ## 2       27          42            19
    ## 3       26          41.4          19
    ## # … with 46 more rows

to reorganize columns:

``` r
select(litters_data, litter_number, pups_survive, everything())
```

    ## # A tibble: 49 × 8
    ##   litter_number pups_survive group gd0_weight gd18_wei…¹ gd_of…² pups_…³ pups_…⁴
    ##   <chr>                <int> <chr>      <dbl>      <dbl>   <int>   <int>   <int>
    ## 1 #85                      3 Con7        19.7       34.7      20       3       4
    ## 2 #1/2/95/2                7 Con7        27         42        19       8       0
    ## 3 #5/5/3/83/3-3            5 Con7        26         41.4      19       6       0
    ## # … with 46 more rows, and abbreviated variable names ¹​gd18_weight,
    ## #   ²​gd_of_birth, ³​pups_born_alive, ⁴​pups_dead_birth

also can use “relocate”:

``` r
relocate(litters_data, litter_number, pups_survive)
```

    ## # A tibble: 49 × 8
    ##   litter_number pups_survive group gd0_weight gd18_wei…¹ gd_of…² pups_…³ pups_…⁴
    ##   <chr>                <int> <chr>      <dbl>      <dbl>   <int>   <int>   <int>
    ## 1 #85                      3 Con7        19.7       34.7      20       3       4
    ## 2 #1/2/95/2                7 Con7        27         42        19       8       0
    ## 3 #5/5/3/83/3-3            5 Con7        26         41.4      19       6       0
    ## # … with 46 more rows, and abbreviated variable names ¹​gd18_weight,
    ## #   ²​gd_of_birth, ³​pups_born_alive, ⁴​pups_dead_birth

## filter

some filter options: gd_of_birth == 20 pups_born_alive \>= 2
pups_survive != 4 !(pups_survive == 4) group %in% c(“Con7”, “Con8”)
group == “Con7” & gd_of_birth == 20

``` r
filter(litters_data, group == "Con7" & gd_of_birth == 20)
```

    ## # A tibble: 4 × 8
    ##   group litter_number   gd0_weight gd18_weight gd_of_b…¹ pups_…² pups_…³ pups_…⁴
    ##   <chr> <chr>                <dbl>       <dbl>     <int>   <int>   <int>   <int>
    ## 1 Con7  #85                   19.7        34.7        20       3       4       3
    ## 2 Con7  #4/2/95/3-3           NA          NA          20       6       0       6
    ## 3 Con7  #2/2/95/3-2           NA          NA          20       6       0       4
    ## 4 Con7  #1/5/3/83/3-3/2       NA          NA          20       9       0       9
    ## # … with abbreviated variable names ¹​gd_of_birth, ²​pups_born_alive,
    ## #   ³​pups_dead_birth, ⁴​pups_survive

also can use `drop`:

remove any row with a missing value:

``` r
drop_na(litters_data)
```

    ## # A tibble: 31 × 8
    ##   group litter_number gd0_weight gd18_weight gd_of_birth pups_…¹ pups_…² pups_…³
    ##   <chr> <chr>              <dbl>       <dbl>       <int>   <int>   <int>   <int>
    ## 1 Con7  #85                 19.7        34.7          20       3       4       3
    ## 2 Con7  #1/2/95/2           27          42            19       8       0       7
    ## 3 Con7  #5/5/3/83/3-3       26          41.4          19       6       0       5
    ## # … with 28 more rows, and abbreviated variable names ¹​pups_born_alive,
    ## #   ²​pups_dead_birth, ³​pups_survive

remove rows for which wt_increase is missing (see p8105 webpage):

## mutate

``` r
mutate(litters_data,
  wt_gain = gd18_weight - gd0_weight,
  group = str_to_lower(group)
)
```

    ## # A tibble: 49 × 9
    ##   group litter_number gd0_weight gd18_…¹ gd_of…² pups_…³ pups_…⁴ pups_…⁵ wt_gain
    ##   <chr> <chr>              <dbl>   <dbl>   <int>   <int>   <int>   <int>   <dbl>
    ## 1 con7  #85                 19.7    34.7      20       3       4       3    15  
    ## 2 con7  #1/2/95/2           27      42        19       8       0       7    15  
    ## 3 con7  #5/5/3/83/3-3       26      41.4      19       6       0       5    15.4
    ## # … with 46 more rows, and abbreviated variable names ¹​gd18_weight,
    ## #   ²​gd_of_birth, ³​pups_born_alive, ⁴​pups_dead_birth, ⁵​pups_survive

## arrange

``` r
head(arrange(litters_data, group, pups_born_alive), 10)
```

    ## # A tibble: 10 × 8
    ##    group litter_number   gd0_weight gd18_weight gd_of_…¹ pups_…² pups_…³ pups_…⁴
    ##    <chr> <chr>                <dbl>       <dbl>    <int>   <int>   <int>   <int>
    ##  1 Con7  #85                   19.7        34.7       20       3       4       3
    ##  2 Con7  #5/4/2/95/2           28.5        44.1       19       5       1       4
    ##  3 Con7  #5/5/3/83/3-3         26          41.4       19       6       0       5
    ##  4 Con7  #4/2/95/3-3           NA          NA         20       6       0       6
    ##  5 Con7  #2/2/95/3-2           NA          NA         20       6       0       4
    ##  6 Con7  #1/2/95/2             27          42         19       8       0       7
    ##  7 Con7  #1/5/3/83/3-3/2       NA          NA         20       9       0       9
    ##  8 Con8  #2/2/95/2             NA          NA         19       5       0       4
    ##  9 Con8  #1/6/2/2/95-2         NA          NA         20       7       0       6
    ## 10 Con8  #3/6/2/2/95-3         NA          NA         20       7       0       7
    ## # … with abbreviated variable names ¹​gd_of_birth, ²​pups_born_alive,
    ## #   ³​pups_dead_birth, ⁴​pups_survive

## %\>%

example 1:

``` r
litters_data_raw = read_csv("./data/FAS_litters.csv",
  col_types = "ccddiiii")
litters_data_clean_names = janitor::clean_names(litters_data_raw)
litters_data_selected_cols = select(litters_data_clean_names, -pups_survive)
litters_data_with_vars = 
  mutate(
    litters_data_selected_cols, 
    wt_gain = gd18_weight - gd0_weight,
    group = str_to_lower(group))
litters_data_with_vars_without_missing = 
  drop_na(litters_data_with_vars, wt_gain)
litters_data_with_vars_without_missing
```

    ## # A tibble: 31 × 8
    ##   group litter_number gd0_weight gd18_weight gd_of_birth pups_…¹ pups_…² wt_gain
    ##   <chr> <chr>              <dbl>       <dbl>       <int>   <int>   <int>   <dbl>
    ## 1 con7  #85                 19.7        34.7          20       3       4    15  
    ## 2 con7  #1/2/95/2           27          42            19       8       0    15  
    ## 3 con7  #5/5/3/83/3-3       26          41.4          19       6       0    15.4
    ## # … with 28 more rows, and abbreviated variable names ¹​pups_born_alive,
    ## #   ²​pups_dead_birth

example 2:

``` r
litters_data_clean = 
  drop_na(
    mutate(
      select(
        janitor::clean_names(
          read_csv("./data/FAS_litters.csv", col_types = "ccddiiii")
          ), 
      -pups_survive
      ),
    wt_gain = gd18_weight - gd0_weight,
    group = str_to_lower(group)
    ),
  wt_gain
  )
```

better approach using `%>%`

``` r
litters_data = 
  read_csv("./data/FAS_litters.csv", col_types = "ccddiiii") %>%
  janitor::clean_names() %>%
  select(-pups_survive) %>%
  mutate(
    wt_gain = gd18_weight - gd0_weight,
    group = str_to_lower(group)) %>% 
  drop_na(wt_gain)
```

using `.`:

``` r
litters_data = 
  read_csv("./data/FAS_litters.csv", col_types = "ccddiiii") %>%
  janitor::clean_names(dat = .) %>%
  select(.data = ., -pups_survive) %>%
  mutate(.data = .,
    wt_gain = gd18_weight - gd0_weight,
    group = str_to_lower(group)) %>% 
  drop_na(data = ., wt_gain)
```

another example:

``` r
litters_data %>%
  lm(wt_gain ~ pups_born_alive, data = .) %>%
  broom::tidy()
```

    ## # A tibble: 2 × 5
    ##   term            estimate std.error statistic  p.value
    ##   <chr>              <dbl>     <dbl>     <dbl>    <dbl>
    ## 1 (Intercept)       13.1       1.27      10.3  3.39e-11
    ## 2 pups_born_alive    0.605     0.173      3.49 1.55e- 3
