
<!---
  Licensed to the Apache Software Foundation (ASF) under one
  or more contributor license agreements.  See the NOTICE file
  distributed with this work for additional information
  regarding copyright ownership.  The ASF licenses this file
  to you under the Apache License, Version 2.0 (the
  "License"); you may not use this file except in compliance
  with the License.  You may obtain a copy of the License at
    http://www.apache.org/licenses/LICENSE-2.0
  Unless required by applicable law or agreed to in writing,
  software distributed under the License is distributed on an
  "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY
  KIND, either express or implied.  See the License for the
  specific language governing permissions and limitations
  under the License.
-->
<!-- README.md is generated from README.Rmd. Please edit that file -->

# adbcpostgresql

<!-- badges: start -->
<!-- badges: end -->

The goal of adbcpostgresql is to provide a low-level developer-facing
interface to the Arrow Database Connectivity (ADBC) PostgreSQL driver.

## Installation

You can install the released version of adbcpostgresql from
[CRAN](https://cran.r-project.org/) with:

``` r
install.packages("adbcpostgresql")
```

You can install the development version of adbcpostgresql from
[GitHub](https://github.com/) with:

``` r
# install.packages("pak")
pak::pak("apache/arrow-adbc/r/adbcpostgresql")
```

ADBC drivers for R use a relatively new feature of pkgbuild to enable
installation from GitHub via pak. Depending on when you installed pak,
you may need to update its internal version of pkgbuild.

``` r
install.packages("pkgbuild", pak:::private_lib_dir())
pak::cache_clean()
```

## Example

This is a basic example which shows you how to solve a common problem:

``` r
library(adbcdrivermanager)

# Use the driver manager to connect to a database
uri <- Sys.getenv("ADBC_POSTGRESQL_TEST_URI")
db <- adbc_database_init(adbcpostgresql::adbcpostgresql(), uri = uri)
con <- adbc_connection_init(db)

# Write a table
nycflights13::flights |> write_adbc(con, "flights")

# Issue a query
con |>
  read_adbc("SELECT * from flights") |>
  tibble::as_tibble()
#> # A tibble: 336,776 × 19
#>     year month   day dep_time sched_dep_time dep_delay arr_time sched_arr_time
#>    <int> <int> <int>    <int>          <int>     <dbl>    <int>          <int>
#>  1  2013     1     1      517            515         2      830            819
#>  2  2013     1     1      533            529         4      850            830
#>  3  2013     1     1      542            540         2      923            850
#>  4  2013     1     1      544            545        -1     1004           1022
#>  5  2013     1     1      554            600        -6      812            837
#>  6  2013     1     1      554            558        -4      740            728
#>  7  2013     1     1      555            600        -5      913            854
#>  8  2013     1     1      557            600        -3      709            723
#>  9  2013     1     1      557            600        -3      838            846
#> 10  2013     1     1      558            600        -2      753            745
#> # ℹ 336,766 more rows
#> # ℹ 11 more variables: arr_delay <dbl>, carrier <chr>, flight <int>,
#> #   tailnum <chr>, origin <chr>, dest <chr>, air_time <dbl>, distance <dbl>,
#> #   hour <dbl>, minute <dbl>, time_hour <dttm>
```

``` r
# Clean up!
con |>
  execute_adbc("DROP TABLE flights")
adbc_connection_release(con)
adbc_database_release(db)
```
