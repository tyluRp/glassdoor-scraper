
<!-- README.md is generated from README.Rmd. Please edit that file -->

# glassdoor-scraper

<!-- badges: start -->

<!-- badges: end -->

A demonstration of scraping glassdoor reviews using `rvest`. Note that
the underlying functions rely on xpath’s that I copied by simply
clicking what I wanted and inspecting the element. These will probably
change over time and consequently, the scripts will fail. As of
2020-08-08, it seems to work pretty well.

``` r
source("R/scrape.R")
#> Loading libraries...
#> Sourcing functions...

# example urls, we'll go with Google
tesla_url <- "https://www.glassdoor.com/Reviews/Tesla-Reviews-E43129"
apple_url <- "https://www.glassdoor.com/Reviews/Apple-Reviews-E1138"
google_url <- "https://www.glassdoor.com/Reviews/Google-Reviews-E9079"

# loop through n pages
pages <- 1:5
out <- lapply(pages, function(page) {
  Sys.sleep(1)
  try_scrape_reviews(google_url, page)
})
#> Scraping page [1] at [2020-08-08 09:13:05]
#> Scraping page [2] at [2020-08-08 09:13:14]
#> Scraping page [3] at [2020-08-08 09:13:17]
#> Scraping page [4] at [2020-08-08 09:13:20]
#> Scraping page [5] at [2020-08-08 09:13:24]

# filter for stuff we successfully extracted
reviews <- bind_rows(Filter(Negate(is.null), out), .id = "page")

reviews %>%
  distinct() %>%
  mutate(
    review_time = clean_review_datetime(review_time_raw),
    page = as.numeric(page)
  ) %>%
  select(
    page,
    review_time,
    review_time_raw,
    review_title,
    employee_role,
    employee_history,
    employeer_pros,
    employeer_cons,
    employeer_rating
  ) %>% 
  glimpse()
#> Rows: 50
#> Columns: 9
#> $ page             <dbl> 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 2, 2, 2, 2, 2, 2, 2, 2…
#> $ review_time      <dttm> 2020-08-07 16:50:50, 2013-06-21 12:42:33, 2014-05-1…
#> $ review_time_raw  <chr> "Fri Aug 07 2020 16:50:50 GMT-0700 (Pacific Daylight…
#> $ review_title     <chr> "Great place to work!", "Moving at the speed of ligh…
#> $ employee_role    <chr> "Current Employee - Software Engineer", "Former Empl…
#> $ employee_history <chr> "I have been working at Google full-time for more th…
#> $ employeer_pros   <chr> "Great perks, great salary, interesting problems, gr…
#> $ employeer_cons   <chr> "Some long hours, but free dinners and lunches make …
#> $ employeer_rating <dbl> 5, 4, 5, 5, 5, 5, 5, 5, 5, 5, 5, 5, 5, 5, 5, 5, 5, 5…
```

## Session Info

``` r
sessioninfo::session_info()
#> ─ Session info ───────────────────────────────────────────────────────────────
#>  setting  value                       
#>  version  R version 4.0.2 (2020-06-22)
#>  os       macOS  10.16                
#>  system   x86_64, darwin17.0          
#>  ui       X11                         
#>  language (EN)                        
#>  collate  en_US.UTF-8                 
#>  ctype    en_US.UTF-8                 
#>  tz       America/Los_Angeles         
#>  date     2020-08-08                  
#> 
#> ─ Packages ───────────────────────────────────────────────────────────────────
#>  package     * version date       lib source        
#>  assertthat    0.2.1   2019-03-21 [1] CRAN (R 4.0.2)
#>  cli           2.0.2   2020-02-28 [1] CRAN (R 4.0.2)
#>  crayon        1.3.4   2017-09-16 [1] CRAN (R 4.0.2)
#>  curl          4.3     2019-12-02 [1] CRAN (R 4.0.1)
#>  digest        0.6.25  2020-02-23 [1] CRAN (R 4.0.2)
#>  dplyr       * 1.0.0   2020-05-29 [1] CRAN (R 4.0.2)
#>  ellipsis      0.3.1   2020-05-15 [1] CRAN (R 4.0.2)
#>  evaluate      0.14    2019-05-28 [1] CRAN (R 4.0.1)
#>  fansi         0.4.1   2020-01-08 [1] CRAN (R 4.0.2)
#>  generics      0.0.2   2018-11-29 [1] CRAN (R 4.0.2)
#>  glue        * 1.4.1   2020-05-13 [1] CRAN (R 4.0.2)
#>  htmltools     0.5.0   2020-06-16 [1] CRAN (R 4.0.2)
#>  httr          1.4.2   2020-07-20 [1] CRAN (R 4.0.2)
#>  knitr         1.29    2020-06-23 [1] CRAN (R 4.0.2)
#>  lifecycle     0.2.0   2020-03-06 [1] CRAN (R 4.0.2)
#>  lubridate   * 1.7.9   2020-06-08 [1] CRAN (R 4.0.2)
#>  magrittr      1.5     2014-11-22 [1] CRAN (R 4.0.2)
#>  pillar        1.4.6   2020-07-10 [1] CRAN (R 4.0.2)
#>  pkgconfig     2.0.3   2019-09-22 [1] CRAN (R 4.0.2)
#>  purrr         0.3.4   2020-04-17 [1] CRAN (R 4.0.2)
#>  R6            2.4.1   2019-11-12 [1] CRAN (R 4.0.2)
#>  Rcpp          1.0.5   2020-07-06 [1] CRAN (R 4.0.2)
#>  rlang         0.4.7   2020-07-09 [1] CRAN (R 4.0.2)
#>  rmarkdown     2.3     2020-06-18 [1] CRAN (R 4.0.2)
#>  rvest       * 0.3.6   2020-07-25 [1] CRAN (R 4.0.2)
#>  sessioninfo   1.1.1   2018-11-05 [1] CRAN (R 4.0.2)
#>  stringi       1.4.6   2020-02-17 [1] CRAN (R 4.0.2)
#>  stringr     * 1.4.0   2019-02-10 [1] CRAN (R 4.0.2)
#>  tibble        3.0.3   2020-07-10 [1] CRAN (R 4.0.2)
#>  tidyselect    1.1.0   2020-05-11 [1] CRAN (R 4.0.2)
#>  utf8          1.1.4   2018-05-24 [1] CRAN (R 4.0.2)
#>  vctrs         0.3.2   2020-07-15 [1] CRAN (R 4.0.2)
#>  withr         2.2.0   2020-04-20 [1] CRAN (R 4.0.2)
#>  xfun          0.15    2020-06-21 [1] CRAN (R 4.0.2)
#>  xml2        * 1.3.2   2020-04-23 [1] CRAN (R 4.0.2)
#>  yaml          2.2.1   2020-02-01 [1] CRAN (R 4.0.2)
#> 
#> [1] /Library/Frameworks/R.framework/Versions/4.0/Resources/library
```
