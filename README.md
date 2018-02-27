
<!-- README.md is generated from README.Rmd. Please edit that file -->

# sparkts

[![Project Status: Active - The project has reached a stable, usable
state and is being actively
developed.](http://www.repostatus.org/badges/latest/active.svg)](http://www.repostatus.org/#active)
[![CRAN\_Status\_Badge](http://www.r-pkg.org/badges/version/sparkts)](http://cran.r-project.org/package=sparkts)
[![Travis-CI Build
Status](https://travis-ci.org/nathaneastwood/sparkts.svg?branch=master)](https://travis-ci.org/nathaneastwood/sparkts)
[![Coverage
Status](https://img.shields.io/codecov/c/github/nathaneastwood/sparkts/master.svg)](https://codecov.io/github/nathaneastwood/sparkts?branch=master)
[![License:
MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

The goal of sparkts is to provide a test bed of `sparklyr` extensions
for the [`spark-ts`](https://github.com/srussell91/SparkTS) framework
which was modified from the
[`spark-timeseries`](https://github.com/sryza/spark-timeseries)
framework.

## Installation

You can install sparkts from github with:

``` r
# install.packages("devtools")
devtools::install_github("nathaneastwood/sparkts")
```

## Example

This is a basic example which shows you how to calculate the standard
error for some time series data:

``` r
# Set up a spark connection
sc <- sparklyr::spark_connect(master = "local", version = "2.1.0")

# Extract some data
std_data <- sparklyr::spark_read_json(
  sc,
  "std_data",
  path = system.file(
    "data_raw/StandardErrorDataIn.json",
    package = "sparkts"
  )
) %>%
  sparklyr::spark_dataframe()

# Calculate the standard error
output <- sdf_standard_error(
  sc,
  std_data,
  x_col = "xColumn",
  y_col = "yColumn",
  z_col = "zColumn",
  new_column_name = "stdError"
)

# Extract the standard error column
output <- output %>% dplyr::collect()
output[["stdError"]]
[1] 10.58512 14.11569 16.79678 19.07034 22.35173 22.91944 24.61259 26.19433

# Disconnect the spark session
sparklyr::spark_disconnect(sc = sc)
```