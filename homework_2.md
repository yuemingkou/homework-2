Homework 2
================

Problem 1
---------

Read and clean the data

``` r
subway_data = 
  read_csv("./data/NYC_Transit_Subway_Entrance_And_Exit_Data.csv") %>% 
  janitor::clean_names() %>%
  select(line:entry, vending, ada) %>% 
  mutate(entry = recode(entry, "YES" = TRUE, "NO" = FALSE))
```

    ## Parsed with column specification:
    ## cols(
    ##   .default = col_character(),
    ##   `Station Latitude` = col_double(),
    ##   `Station Longitude` = col_double(),
    ##   Route8 = col_integer(),
    ##   Route9 = col_integer(),
    ##   Route10 = col_integer(),
    ##   Route11 = col_integer(),
    ##   ADA = col_logical(),
    ##   `Free Crossover` = col_logical(),
    ##   `Entrance Latitude` = col_double(),
    ##   `Entrance Longitude` = col_double()
    ## )

    ## See spec(...) for full column specifications.

The dataset contains line, station name, station latitude and longitude, routes served, entry, vending, entrance type and ADA compliance. My data cleaning steps so far include: load the data and clean up the column names, select columns that I want to keep, and convert the entry variable from character to a logical variable. The dimension (rows x columns) of the resulting dataset is 1868, 19. These data are not tidy: the route number is spread across 11 columns.

Answer the following questions using these data:

``` r
distinct_data = distinct(subway_data, line, station_name, .keep_all = TRUE)
```

How many distinct stations are there?

There are 465 distinct stations.

How many stations are ADA compliant?

There are 85 stations are ADA compliant.

What proportion of station entrances / exits without vending allow entrance?

``` r
distinct_data = mutate(distinct_data, vending = recode(vending, "YES" = TRUE, "NO" = FALSE))
```

The proportion of station entrances / exits without vending allow entrance is 0.0193548

Reformat data so that route number and route name are distinct variables.

``` r
distinct_tidy = gather(distinct_data, key = route_number, value = route_name, 
                       route1:route11, na.rm = TRUE)
```

How many distinct stations serve the A train? Of the stations that serve the A train, how many are ADA compliant?

60 distinct stations serve the A train.

Of the stations that serve the A train, 17 are ADA compliant.

Problem 2
---------
