# Sample Scripts for downsampling

+ every 1 hour mean downsampling
```
import "strings"

from(bucket: "h-jasin")
    |> range(start: tYest58MmssLoc, stop: tNowFwd1dLoc)
    |> filter(fn: (r) => r._measurement =~ /(?i)AHU_OT1/)
    |> filter(fn: (r) => r._field =~ /(?i)ind_(chws|chwr|sa)_tp/ or r._field =~ /(?i)ind_room_(tp|rh|prs)/ or r._field =~ /(?i)ind_(chwv|heat)_out/)
    |> aggregateWindow(every: 1h, fn: mean, createEmpty: false)
    |> map(fn: (r) => ({r with _measurement: "OT_1h_mean"}))
    |> map(fn: (r) => ({r with _field: strings.replace(v: r._field, t: "IND", u: "OT1", i: 1)}))
    |> to(bucket: "h-jasin_ds", org: "houkinyau")
    |> yield(name: "OT1")
```
+ every 15 minutes mean downsampling

```
import "strings"

from(bucket: "h-jasin")
    |> range(start: tYest58MmssLoc, stop: tNowFwd1dLoc)
    |> filter(fn: (r) => r._measurement =~ /(?i)AHU_OT1/)
    |> filter(fn: (r) => r._field =~ /(?i)ind_(chws|chwr|sa)_tp/ or r._field =~ /(?i)ind_room_(tp|rh|prs)/ or r._field =~ /(?i)ind_(chwv|heat)_out/)
    |> aggregateWindow(every: 15m, fn: mean, createEmpty: false)
    |> map(fn: (r) => ({r with _measurement: "OT_15m_mean"}))
    |> map(
        fn: (r) => ({r with
            _field: strings.replace(
                v: r._field,
                t: "IND",
                u: "OT1",
                i: 1,
            ),
        }),
    )
    |> to(bucket: "h-jasin_ds", org: "houkinyau")
    |> yield(name: "OT1")
```

+ every 15 minutes state count,  to change the *_value*

```
import "strings"
from(bucket: "h-jasin")
    |> range(start: tYest58MmssLoc, stop: tNowFwd1dLoc)
    |> filter(fn: (r) => r._measurement =~ /(?i)AHU_OT1/)
    |> filter(fn: (r) => r._field =~ /(?i)IND_AHU_RUN/)
    |> stateCount(fn: (r) => r._value =~ /(?i)^stop/, column: "resetCount")
    |> stateCount(fn: (r) => r._value =~ /(?i)^run/, column: "setCount")
    |> filter(fn: (r) => r.resetCount == 1 or r.setCount == 1)
    |> drop(columns: ["resetCount", "setCount"])
    |> map(fn: (r) => ({r with _measurement: "OT_15m_stateCount"}))
    |> map(fn: (r) => ({r with _field: strings.replace(v: r._field, t: "IND", u: "OT1", i: 1)}))
    |> to(bucket: "h-jasin_ds", org: "houkinyau")
    |> yield(name: "OT1_AHU_RUN")
```