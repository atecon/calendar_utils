# Calendar Utilities

Collection of date/time related tools for convenience when working with date strings and date string series in gretl.

Most functions are small wrappers around gretl's built-in strptime()/strftime() utilities and other date helpers. Please ask questions and report bugs on the gretl mailing list or by creating an issue on the project repository:

https://github.com/atecon/calendar_utils

## Public functions

### date_to_iso8601(date, date_format)

Arguments:

- `date`: string — a date string
- `date_format`: string — format of `date`, e.g. `%Y-%m-%d`

Return:

A scalar integer in numeric ISO8601 format (YYYYMMDD) on success; zero (FALSE) on error. Internally this uses gretl's `strptime()` and `strftime()`.

**Warning:** Prior to gretl 2021e, a bug in `strptime()` produced incorrect results if the input omitted the day of month. If you run gretl 2021d or earlier, ensure date strings include a day. Since 2021e it's acceptable to provide year-only or year+month values, but the fields in the date string must match `date_format`.

Reference:
https://gretlml.univpm.it/hyperkitty/list/gretl-devel@gretlml.univpm.it/message/6ENWKDGSYB32ZFKHENLPFJSS3X22JGYB/

---

### dates_to_iso8601(dates, date_format)

Arguments:

- `dates`: series — a series of date strings
- `date_format`: string — format of the entries in `dates`, e.g. `%Y-%m-%d`

Return:

A series of numeric ISO8601 dates (YYYYMMDD). For each entry, the function returns the numeric date if casting succeeds and zero (FALSE) on error.

See the warning on `date_to_iso8601()` above regarding `strptime()` behavior in older gretl versions.

---

### iso8601_to_dates(dates)

Arguments:

- `dates`: series — series of numeric ISO8601 dates

Return:

A string array (strings) with dates converted to extended ISO8601 format (`YYYY-MM-DD`) by default. Missing or invalid input entries are returned as empty strings. This function is the inverse of `dates_to_iso8601()` for common cases.

---

### create_iso8601_series(init_date)

Removed since v0.5.

Use gretl's built-in obs/date facilities instead. For example, to set a daily time series structure:

```
setobs 7 2020-01-01 --time-series
```

For a panel with daily frequency:

```
setobs 7 2020-01-01 --panel-time
```

The numeric ISO8601 series is available as the dataset accessor `$obsdate`.

---

### numeric_to_extended_iso8601(date)

Arguments:

- `date`: int — numeric ISO8601 date in format `YYYYMMDD`

Return:

A date string in extended ISO8601 format (`YYYY-MM-DD`) on success; an empty string on invalid input.

---

### gdate_to_iso8601(date, frequency[null])

Arguments:

- `date`: string — date string in gretl formats such as `%Y:%m` or `%Y.%m`
- `frequency`: string (optional) — either `monthly` or `quarterly`. If omitted, the function attempts to determine periodicity from the active dataset.

Return:

Numeric ISO8601 integer (YYYYMMDD) for monthly or quarterly input. Uses gretl's `strptime()`/`strftime()` internally.

---

### datetime_components(ts, format[null])

Extract date/time components from datetime (timestamp) strings.

Arguments:

- `ts`: strings — array of datetime strings (timestamps)
- `format`: string, optional — format of the timestamp; default is `%Y-%m-%d %H:%M`

Return:

A bundle containing these elements:

- `date`: strings — dates in extended ISO8601 (`%Y-%m-%d`)
- `second1970`: matrix — column vector of seconds since 1970 (UTC), per gretl's `strptime()` semantics
- `time`: strings — time strings in `%H:%M` or `%H:%M:%s` when seconds are present
- `year`, `month`, `day`, `hour`, `minute`: matrices — column vectors with the respective numeric components
- `second`: matrix — column vector with seconds if available


## Changelog (highlights)

- v0.7, September 2025: Help text as markdown document: improved formatting and structure; Raise min. Gretl version to 2023a
- v0.6, January 2023: Internal improvements
- v0.5, December 2022:
  - Add `datetime_components()`
  - Remove `create_iso8601_series()` and add guidance for `setobs` usage
  - Improve error handling notes for `iso8601_to_dates()`; both `dates_to_iso8601()` and `date_to_iso8601()` return zero on parse error
- v0.4, January 2022: Added `iso8601_to_dates()`
- v0.3, October 2021: Improved help text and added `gdate_to_iso8601()`
- v0.2, October 2020: Added `numeric_to_extended_iso8601()`
- v0.1, October 2020: Initial release
