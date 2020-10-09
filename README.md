# The "calendar_utils" package for Gretl
Collection of useful date time related tools. Most functions are convenience functions for working with date strings and date string series.

The package comprises three public functions which are described in the following.

# Installation
The package can be downloaded and loaded from the official gretl package server via the command:
```
pkg install calendar_utils    # Download once
include calendar_utils.gfn    # Load into memory
```

# Public functions

## ```date_to_iso8601()```
The function ```date_to_iso8601()``` casts a date string to the numeric ISO8601 format. Here are examples on how to use this function:

```
string date1 = "2020-09-03"
string date2 = "20200903"
string date3 = "03-09-2020"
string date4 = "03/09/2020"

scalar iso1 = date_to_iso8601(date1, "%Y-%m-%d")
scalar iso2 = date_to_iso8601(date2, "%Y%m%d")
scalar iso3 = date_to_iso8601(date3, "%d-%m-%Y")
scalar iso4 = date_to_iso8601(date4, "%d/%m/%Y")

print iso1 iso2 iso3 iso4
```

The following scalar values are printed:
```
           iso1 =  20200903.

           iso2 =  20200903.

           iso3 =  20200903.

           iso4 =  20200903.
```

## ```dates_to_iso860```
The function ```dates_to_iso8601()``` casts the date strings of a series to the numeric ISO8601 format. It works for all gretl data sets namely cross-sectional, time-series and panel data sets.

Here are examples on how to use this function. First, we create tiny dummy panel data set with two cross-sectional units and for each we have a time-series of length three. Series ```z``` is a string-values series holding some date strings. These date strings are cast to the ISO8601 format.

```
nulldata 6
scalar T = 3
setobs T 1:1 --stacked-time-series
series x = create_iso8601_series(20200101)

series z = seq(1, 3)' | seq(1, 3)'
strings dates = defarray("2020-09-01", "2020-09-02", "2020-09-03")
stringify(z, dates)

series y = dates_to_iso8601(z, "%Y-%m-%d")
print z y -o
```

The output is as follows:
```
1:1   2020-09-01     20200901
1:2   2020-09-02     20200902
1:3   2020-09-03     20200903
2:1   2020-09-01     20200901
2:2   2020-09-02     20200902
2:3   2020-09-03     20200903

```

## ```create_iso8601_series```
Sometimes one wants to create a series object holding dates. This function takes as a single argument a starting date in the ISO8601 format, and creates a chronological sequence of dates depending on the length of the data set. This function again works for all three gretl data sets supported.

Using the same dummy panel data set as before, here is an example:

```
series x = create_iso8601_series(20200101)
print x -o
```

This yields:
```
               x

1:1     20200101
1:2     20200102
1:3     20200103
2:1     20200101
2:2     20200102
2:3     20200103
```

# Tests
Unit tests can be found in the file ```./tests/run_tests.inp``` and executed on linux through the shell script ```./run_tests.sh```.

# Changelog
- v0.1, October 2020:
    + initial release
