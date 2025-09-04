# Calendar Utilities

Collection of date/time related tools for convenience when working with date strings and date string series in gretl.

Most functions are small wrappers around gretl's built-in strptime()/strftime() utilities and other date helpers. Please ask questions and report bugs on the gretl mailing list or by creating an issue on the project repository:

https://github.com/atecon/calendar_utils

# Public functions

```
date_to_iso8601(date, date_format)
```

*Arguments:*

- `date`: string — a date string
- `date_format`: string — format of `date`, e.g. `%Y-%m-%d`

*Return:*

A scalar integer in numeric ISO8601 format (YYYYMMDD) on success; zero (FALSE) on error. Internally this uses gretl's `strptime()` and `strftime()`.

---

```
dates_to_iso8601(dates, date_format)
```

*Arguments:*

- `dates`: series — a series of date strings
- `date_format`: string — format of the entries in `dates`, e.g. `%Y-%m-%d`

*Return:*

A series of numeric ISO8601 dates (YYYYMMDD). For each entry, the function returns the numeric date if casting succeeds and zero (FALSE) on error.

See the warning on `date_to_iso8601()` above regarding `strptime()` behavior in older gretl versions.

---

```
iso8601_to_string(value, target_format[null])
```

*Arguments:*

- `value`: numeric — numeric ISO8601 date (scalar, series, or column-vector matrix). Examples: `20200903`, `19900101`.
- `target_format`: string (optional) — target strftime-style format for the output strings. Default: `%Y-%m-%d`.

*Return:*

- `strings`: an array with the date(s) formatted according to `target_format`.
- Invalid or unparsable inputs produce an empty string at the corresponding position and a warning may be printed.

*Notes:*

- Accepts and handles scalar, series and column-vector matrix inputs. For series, missing values are flagged and empty strings are returned for problematic entries.
- This function **supersedes older helpers** such as `numeric_to_extended_iso8601()` and `iso8601_to_dates()` by providing flexible output formatting and unified handling of input types.

---

```
iso8601_to_dates(dates) -- SUPERSEDED BY iso8601_to_string()
```

*Arguments:*

- `dates`: series — series of numeric ISO8601 dates

*Return:*

A string array (strings) with dates converted to extended ISO8601 format (`YYYY-MM-DD`) by default. Missing or invalid input entries are returned as empty strings. This function is the inverse of `dates_to_iso8601()` for common cases.

---

```
numeric_to_extended_iso8601(date) -- SUPERSEDED BY iso8601_to_string()
```

*Arguments:*

- `date`: int — numeric ISO8601 date in format `YYYYMMDD`

*Return:*

A date string in extended ISO8601 format (`YYYY-MM-DD`) on success; an empty string on invalid input.

---

```
gdate_to_iso8601(date, frequency[null])
```

*Arguments:*

- `date`: string — date string in gretl formats such as `%Y:%m` or `%Y.%m`
- `frequency`: string (optional) — either `monthly` or `quarterly`. If omitted, the function attempts to determine periodicity from the active dataset.

*Return:*

Numeric ISO8601 integer (YYYYMMDD) for monthly or quarterly input. Uses gretl's `strptime()`/`strftime()` internally.

---

```
datetime_components(ts, format[null])
```

Extract date/time components from datetime (timestamp) strings.

*Arguments:*

- `ts`: strings — array of datetime strings (timestamps)
- `format`: string, optional — format of the timestamp; default is `%Y-%m-%d %H:%M`

*Return:*

A bundle containing these elements:

- `date`: strings — dates in extended ISO8601 (`%Y-%m-%d`)
- `second1970`: matrix — column vector of seconds since 1970 (UTC), per gretl's `strptime()` semantics
- `time`: strings — time strings in `%H:%M` or `%H:%M:%s` when seconds are present
- `year`, `month`, `day`, `hour`, `minute`: matrices — column vectors with the respective numeric components
- `second`: matrix — column vector with seconds if available

---

```
iso8601_to_period_labels(value, frequency[null], quiet[TRUE])
```

*Arguments:*

- `value`: numeric — scalar, series, or column-vector matrix holding ISO8601 integers (YYYYMMDD)
- `frequency`: string (optional) — one of `monthly`, `quarterly`, or `auto` (default). When `auto` the function attempts to infer periodicity: for series it uses the dataset frequency ($pd or $panelpd); otherwise a month-scan heuristic is used. An explicit `monthly` or `quarterly` value overrides auto-detection.
- `quiet`: boolean (optional, default = TRUE) — if FALSE, emit warnings for entries that cannot be converted.

*Return:*

- `strings`: an array of compact period labels. For monthly output labels are of the form `YYYYmM` (e.g. `2023m3`); for quarterly output labels are of the form `YYYYqQ` (e.g. `2023q1`). Missing or invalid inputs produce an empty string at the corresponding position.

*Notes:*

- Accepts scalar, series and (column) matrix inputs. For series the dataset periodicity ($pd for time series or $panelpd for panel) is consulted when `frequency` is `auto`.
- Explicit `frequency` values take precedence over auto-detection.
- The function returns empty strings for invalid months or unsupported frequencies; set `quiet=false` to see warnings.

*Examples:*

- Scalar monthly (auto-detect):

```
iso8601_to_period_labels(20230315)  # -> {"2023m3"}
```

- Scalar explicit quarterly:

```
iso8601_to_period_labels(20230401, "quarterly")  # -> {"2023q2"}
```

- Series / dataset-aware auto-detection (uses $pd or $panelpd):

```
setobs 4 2020:1 --time-series
series iso = $obsdate
strings labs = iso8601_to_period_labels(iso)  # quarterly labels
```

- Matrix / column-vector input with NA handling:

```
matrix m = {20230101; NA; 20230301}
strings labs = iso8601_to_period_labels(m)  # -> {"2023m1", "", "2023m3"}
```

# Changelog

## v0.7, September 2025:

- Add new iso8601_to_string() function superseding numeric_to_extended_iso8601() and iso8601_to_dates()
- Add new iso8601_to_period_labels() function for converting ISO8601 dates to period labels (e.g., "2022q1" or "2022m9")
- Help text as markdown document: improved formatting and structure
- Raise min. Gretl version to 2023a
- Bugfix: refactor datetime_components() function to improve variable declarations and add type safety

## v0.6, January 2023:

- Internal improvements

## v0.5, December 2022:

- Add `datetime_components()`
- Remove `create_iso8601_series()` and add guidance for `setobs` usage
- Improve error handling notes for `iso8601_to_dates()`; both `dates_to_iso8601()` and `date_to_iso8601()` return zero on parse error

## v0.4, January 2022:

- Added `iso8601_to_dates()`

## v0.3, October 2021:

- Improved help text and added `gdate_to_iso8601()`

## v0.2, October 2020:

- Added `numeric_to_extended_iso8601()`

## v0.1, October 2020:

- Initial release
