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

Read and clean the Mr.Trash Wheel sheet:

``` r
library(readxl)
library(cellranger)
mr_trash_data = 
  read_excel("./data/HealthyHarborWaterWheelTotals2017-9-26.xlsx", sheet = 1,
             range = cell_cols("A:N")) %>% 
  janitor::clean_names() %>% 
  filter(!is.na(dumpster), month != "Grand Total") %>% 
  mutate(sports_balls = round(sports_balls), 
         sports_balls = as.integer(sports_balls))
```

Read and clean precipitation data for 2016 and 2017:

``` r
precipitation_2016 =
  read_excel("./data/HealthyHarborWaterWheelTotals2017-9-26.xlsx", sheet = 4,
             range = "A2:B14") %>% 
  janitor::clean_names() %>% 
  filter(!is.na(total)) %>% 
  mutate(year = "2016")

precipitation_2017 =
  read_excel("./data/HealthyHarborWaterWheelTotals2017-9-26.xlsx", sheet = 3,
             range = "A2:B14") %>% 
  janitor::clean_names() %>% 
  filter(!is.na(total)) %>% 
  mutate(year = "2017")
```

Combine datasets and convert month to a character variable:

``` r
precipitation_data = 
  bind_rows(precipitation_2016, precipitation_2017) %>% 
  mutate(month = month.name[month])
```

The number of observations in "mr\_trash\_data" is 215. The number of observations in "precipitation\_data" is 20.

Examples of key variables:

The total precipitation in 2017 is 29.93. The median number of sports balls in a dumpster in 2016 is 26.

Problem 3
---------

load the data from the p8105.datasets package:

``` r
devtools::install_github("p8105/p8105.datasets")
```

    ## Skipping install of 'p8105.datasets' from a github remote, the SHA1 (21f5ad1c) has not changed since last install.
    ##   Use `force = TRUE` to force installation

``` r
library(p8105.datasets)
```

clean the data:

``` r
brfss_data = 
  brfss_smart2010 %>% 
  janitor::clean_names() %>% 
  filter(topic == "Overall Health") %>% 
  select(-class, -topic, -question, -sample_size, 
         -(confidence_limit_low:geo_location)) %>% 
  spread(key = response, value = data_value) %>% 
  janitor::clean_names() %>% 
  select(year, locationabbr, locationdesc, excellent, very_good, good, fair, poor) %>% 
  mutate(prop_exc_vg = excellent + very_good)
```

1）How many unique locations are included in the dataset? Is every state represented? What state is observed the most?

``` r
state_count = 
  brfss_data %>% 
  distinct(locationdesc, .keep_all = TRUE) %>% 
  count(locationabbr) %>% 
  arrange(desc(n))
state_count
```

    ## # A tibble: 51 x 2
    ##    locationabbr     n
    ##    <chr>        <int>
    ##  1 FL              44
    ##  2 TX              20
    ##  3 NJ              19
    ##  4 NC              16
    ##  5 OK              15
    ##  6 SC              15
    ##  7 WA              15
    ##  8 MD              14
    ##  9 PA              14
    ## 10 CA              12
    ## # ... with 41 more rows

404 unique locations are included in the dataset. From state\_count, we can see every state is represented. FL is observed the most.

2）In 2002, what is the median of the “Excellent” response value?

``` r
brfss_2002 = filter(brfss_data, year == 2002)
```

In 2012, the median of the "Excellent" response value is 23.6

3）Make a histogram of “Excellent” response values in the year 2002:

``` r
ggplot(brfss_2002, aes(x = excellent)) + geom_histogram()
```

    ## `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.

    ## Warning: Removed 2 rows containing non-finite values (stat_bin).

![](homework_2_files/figure-markdown_github/unnamed-chunk-12-1.png)

4）Make a scatterplot showing the proportion of “Excellent” response values in New York County and Queens County (both in NY State) in each year from 2002 to 2010:

``` r
brfss_data %>% 
  filter(locationdesc %in% c("NY - New York County", "NY - Queens County")) %>% 
  ggplot(aes(x = year, y = excellent)) + geom_point(aes(color = locationdesc))
```

![](homework_2_files/figure-markdown_github/unnamed-chunk-13-1.png)
