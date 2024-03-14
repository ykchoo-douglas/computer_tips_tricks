# Date and Time change in programming

```
import "date"
import "experimental"

// 2024-02-06 09:14:44.966
tNow = now()

// 2024-02-06 09:00:00.000
tNow00MmSs = date.truncate(t: tNow, unit: 1h)

// 2024-02-06 08:00:00.000
tNow00HhMmSs = date.truncate(t: tNow, unit: 1d)

// 2024-02-06 00:00:00.000
tNow00HhMmSsLoc = experimental.addDuration(d: -8h, to: tNow00HhMmSs)

// 2024-02-05 23:58:00.000
tYest58MmssLoc = experimental.addDuration(d: -2m, to: tNow00HhMmSsLoc)

// 2024-02-07 08:00:00.000
tNowFwd1d = experimental.addDuration(d: 1d, to: tNow00HhMmSs)

// 2024-02-07 00:00:00.000
tNowFwd1dLoc = experimental.addDuration(d: 1d, to: tNow00HhMmSsLoc)

// 2024-02-06 09:14:44.966
tStopSelected = now()

// 2024-02-01 08:00:00.000
tStopRev1mo = date.truncate(t: tStopSelected, unit: 1mo)

// 2024-02-01 00:00:00.000
tStopRev1moLoc = experimental.addDuration(d: -8h, to: tStopRev1mo)
```