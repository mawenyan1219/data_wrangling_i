tidy data
================
2022-09-27

``` r
pulse_df = haven::read_sas("./data/public_pulse_data.sas7bdat") %>% 
janitor::clean_names() %>% 
    pivot_longer(
    bdi_score_bl:bdi_score_12m,
    names_to = "visit", #this is for column name
    values_to = "bdi", #this is the value in the data drame
    names_prefix = "bdi_score_"
  ) %>% 
  mutate(
    visit = replace(visit, visit == 'bl', '00m'),
    visit = factor(visit)
  ) %>% 
  arrange(id, visit)

pulse_df
```

    ## # A tibble: 4,348 × 5
    ##      id   age sex   visit   bdi
    ##   <dbl> <dbl> <chr> <fct> <dbl>
    ## 1 10003  48.0 male  00m       7
    ## 2 10003  48.0 male  01m       1
    ## 3 10003  48.0 male  06m       2
    ## 4 10003  48.0 male  12m       0
    ## 5 10015  72.5 male  00m       6
    ## # … with 4,343 more rows

``` r
litters_df = read_csv("data/FAS_litters.csv") %>%
  janitor::clean_names() %>% 
  select(litter_number, ends_with("weight")) %>% 
  pivot_longer(
    gd0_weight:gd18_weight,
    names_to = "gd", 
    values_to = "weight") %>% 
  mutate(gd = recode(gd, "gd0_weight" = 0, "gd18_weight" = 18))
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
analysis_result = tibble(
  group = c("treatment", "treatment", "placebo", "placebo"),
  time = c("pre", "post", "pre", "post"),
  mean = c(4, 8, 3.5, 4)
)

analysis_result
```

    ## # A tibble: 4 × 3
    ##   group     time   mean
    ##   <chr>     <chr> <dbl>
    ## 1 treatment pre     4  
    ## 2 treatment post    8  
    ## 3 placebo   pre     3.5
    ## 4 placebo   post    4

``` r
analysis_result_wide = pivot_wider(
  analysis_result, 
  names_from = "time", 
  values_from = "mean")
```

``` r
fellowship_ring = 
  readxl::read_excel("./data/LotR_Words.xlsx", range = "B3:D6") %>%
  mutate(movie = "fellowship_ring")

two_towers = 
  readxl::read_excel("./data/LotR_Words.xlsx", range = "F3:H6") %>%
  mutate(movie = "two_towers")

return_king = 
  readxl::read_excel("./data/LotR_Words.xlsx", range = "J3:L6") %>%
  mutate(movie = "return_king")

lotr_tidy = 
  bind_rows(fellowship_ring, two_towers, return_king) %>%
  janitor::clean_names() %>%
  pivot_longer(
    female:male,
    names_to = "gender", 
    values_to = "words") %>%
  mutate(race = str_to_lower(race)) %>% 
  select(movie, everything()) 

lotr_tidy
```

    ## # A tibble: 18 × 4
    ##    movie           race   gender words
    ##    <chr>           <chr>  <chr>  <dbl>
    ##  1 fellowship_ring elf    female  1229
    ##  2 fellowship_ring elf    male     971
    ##  3 fellowship_ring hobbit female    14
    ##  4 fellowship_ring hobbit male    3644
    ##  5 fellowship_ring man    female     0
    ##  6 fellowship_ring man    male    1995
    ##  7 two_towers      elf    female   331
    ##  8 two_towers      elf    male     513
    ##  9 two_towers      hobbit female     0
    ## 10 two_towers      hobbit male    2463
    ## 11 two_towers      man    female   401
    ## 12 two_towers      man    male    3589
    ## 13 return_king     elf    female   183
    ## 14 return_king     elf    male     510
    ## 15 return_king     hobbit female     2
    ## 16 return_king     hobbit male    2673
    ## 17 return_king     man    female   268
    ## 18 return_king     man    male    2459

``` r
pup_data = 
  read_csv("./data/FAS_pups.csv") %>%
  janitor::clean_names() %>%
  mutate(
    sex = recode(sex, `1` = "male", `2` = "female"),
    sex = factor(sex)) 
```

    ## Rows: 313 Columns: 6
    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: ","
    ## chr (1): Litter Number
    ## dbl (5): Sex, PD ears, PD eyes, PD pivot, PD walk
    ## 
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

``` r
litter_data = 
  read_csv("./data/FAS_litters.csv") %>%
  janitor::clean_names() %>%
  separate(group, into = c("dose", "day_of_tx"), sep = 3) %>%
  relocate(litter_number) %>%
  mutate(
    wt_gain = gd18_weight - gd0_weight,
    dose = str_to_lower(dose))
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
fas_data = 
  left_join(pup_data, litter_data, by = "litter_number")

fas_data
```

    ## # A tibble: 313 × 15
    ##   litter_n…¹ sex   pd_ears pd_eyes pd_pi…² pd_walk dose  day_o…³ gd0_w…⁴ gd18_…⁵
    ##   <chr>      <fct>   <dbl>   <dbl>   <dbl>   <dbl> <chr> <chr>     <dbl>   <dbl>
    ## 1 #85        male        4      13       7      11 con   7          19.7    34.7
    ## 2 #85        male        4      13       7      12 con   7          19.7    34.7
    ## 3 #1/2/95/2  male        5      13       7       9 con   7          27      42  
    ## 4 #1/2/95/2  male        5      13       8      10 con   7          27      42  
    ## 5 #5/5/3/83… male        5      13       8      10 con   7          26      41.4
    ## # … with 308 more rows, 5 more variables: gd_of_birth <dbl>,
    ## #   pups_born_alive <dbl>, pups_dead_birth <dbl>, pups_survive <dbl>,
    ## #   wt_gain <dbl>, and abbreviated variable names ¹​litter_number, ²​pd_pivot,
    ## #   ³​day_of_tx, ⁴​gd0_weight, ⁵​gd18_weight
