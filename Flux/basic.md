# Flux basic

## Query InfluxDB

Every Flux query needs the following:

1. A data source
2. A time range
3. Data filters

Flux’s `from()` function defines an InfluxDB data source. It requires a bucket parameter.

```
from(bucket:"example-bucket")
```

Flux requires `a time range` when querying time series data. `"Unbounded”` queries are very `resource-intensive` and as a protective measure, Flux `will not query the database without a specified range.`

Use the `pipe-forward operator (|>)` to pipe data from your data source into `range()`

`range()` accepts two parameters: `start` and `stop`. Start and stop values can be `relative` using negative durations or `absolute` using timestamps.

```
// Relative time range with start only. Stop defaults to now.
from(bucket:"example-bucket")
    |> range(start: -1h)

// Relative time range with start and stop
from(bucket:"example-bucket")
    |> range(start: -1h, stop: -10m)
```

> Relative ranges are relative to “now.”

```
// absolute time range
from(bucket:"example-bucket")
    |> range(start: 2021-01-01T00:00:00Z, stop: 2021-01-01T12:00:00Z)
```

Pass your ranged data into `filter()` to `narrow results` based on `data attributes` or `columns`. `filter() has one parameter, fn`, which expects a `predicate function` evaluates rows by column values.

```
from(bucket: "example-bucket")
    |> range(start: -15m)
    |> filter(fn: (r) => r._measurement == "cpu" and r._field == "usage_system" and r.cpu == "cpu-total")
```

```
// using Regex in filter predicate function. Regex format follows golang
from(bucket: "example-bucket")
    |> range(start: -15m)
    |> filter(fn: (r) => r._measurement =~ /(?i)cpu/ and r._field =~ /(?i)usage_system/ and r.cpu =~ /(?i)cpu-total/)
```

`Explicitly calling yield()` is only necessary when including `multiple queries in the same Flux query`.

```
from(bucket: "example-bucket")
    |> range(start: -15m)
    |> filter(fn: (r) => r._measurement == "cpu" and r._field == "usage_system" and r.cpu == "cpu-total")
    |> yield(name: "CPU")
```

## Transform data

Common examples are aggregating data, downsampling data

creating a Flux script that `partitions` data into `windows of time`, `averages` the _values in `each window`, and `outputs` the averages as `a new table`.

+ partitions data into windows of time
```    
    ...
    |> filter(fn: (r) => r._measurement == "cpu" and r._field == "usage_system" and r.cpu == "cpu-total")
    |> window(every: 5m)
```

+ averages the _values in each window
```  
    ...
    |> filter(fn: (r) => r._measurement == "cpu" and r._field == "usage_system" and r.cpu == "cpu-total")
    |> window(every: 5m)
    |> mean()
```

As values are aggregated, the resulting tables `do not have a _time` column because the records used for the aggregation all have `different timestamps`. Therefore the `_time` column is `dropped`. But a _time column is required in the next operation. Use the `duplicate()` function to duplicate the `_stop` column as the `_time` column for `each windowed` table.

```
    ...
    |> filter(fn: (r) => r._measurement == "cpu" and r._field == "usage_system" and r.cpu == "cpu-total")
    |> window(every: 5m)
    |> mean()
    |> duplicate(column: "_stop", as: "_time")
```

+ gather all points into a single, infinite window.

```
    ...
    |> filter(fn: (r) => r._measurement == "cpu" and r._field == "usage_system" and r.cpu == "cpu-total")
    |> window(every: 5m)
    |> mean()
    |> duplicate(column: "_stop", as: "_time")
    |> window(every: inf)
```

+ helper function to replace above queries. `aggregateWindow()`

```
    ...
    |> filter(fn: (r) => r._measurement == "cpu" and r._field == "usage_system" and r.cpu == "cpu-total")
    |> aggregateWindow(every: 5m, fn: mean)
```


