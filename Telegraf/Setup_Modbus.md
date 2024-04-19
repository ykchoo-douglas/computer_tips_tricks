# Setup Modbus Input Plugin

### Step 1 - Download and install the Telegraf

[telegraf_install]: https://docs.influxdata.com/telegraf/v1/install/
[telegraf_begin]: https://docs.influxdata.com/telegraf/v1/get-started/

1. [Install Telegraf][telegraf_install]
2. [Get started][telegraf_begin]

### Step 2 - Create the Configuration

[modbus_plugin]: https://github.com/influxdata/telegraf/blob/master/plugins/inputs/modbus/README.md#metric-configuration-style
[general_conf]: https://github.com/influxdata/telegraf/blob/master/docs/CONFIGURATION.md
[telegraf_service]: https://github.com/influxdata/telegraf/blob/master/docs/WINDOWS_SERVICE.md

1. [Modbus Input Plugin][modbus_plugin]
2. [Configuration][general_conf]
3. [Running Telegraf as a Windows Service][telegraf_service]

the directory normally store the configuration

```shell
C:\Program Files\InfluxData\Telegraf\
```

the default/complete configuration can create using the command

```shell
> telegraf.exe --sample-config > telegraf.conf
```

if want to hide the token, the following command to run in Power Shell

```shell
$env:INFLUX_TOKEN = "<token>"
```

sample of configuration

```
[agent]
    interval = "10s"
    round_interval = true
    metric_batch_size = 1000
    metric_buffer_limit = 10000
    collection_jitter = "0s"
    flush_interval = "10s"
    flush_jitter = "0s"
    precision = ""
    hostname = ""
    omit_hostname = true
    debug = false
    quiet = true
    # view from windows event viewer
    logtarget = "eventlog"
    # logfile = "C:\telegraf.log"

[[outputs.influxdb_v2]]
    urls = ["http://localhost:8086"]
    token = "eq8XNOobd7KHsDI936iZsRON3SQQ2bQ9askrFWZ8IEBmk_t3lbso4AEQR_rG60q1UjM97S6PpmzxGqnGH56_Fw=="
    organization = "houkinyau"
    bucket = "hospital"

[[inputs.modbus]]
    name = "AHU_OT1"
    slave_id = 1
    timeout = "3s"
    controller = "tcp://localhost:502"
    configuration_type = "request"

    ## --- "request" configuration style ---
    [[inputs.modbus.request]]
        slave_id = 1
        byte_order = "ABCD"
        register = "coil"
        measurement = "modbus"
        optimization = "none"

        ## Coil / discrete input example
        fields = [
            { address=0, name="motor1_run" },
            { address=1, name="jog", measurement="motor" },
            { address=2, name="motor1_stop", omit=true },
            { address=3, name="motor1_overheating", output="BOOL" },
        ]

        [inputs.modbus.request.tags]
            machine = "impresser"
            location = "main building"

    [[inputs.modbus.request]]
        slave_id = 1
        byte_order = "ABCD"
        register = "holding"

        ## Holding example
        ## All of those examples will result in FLOAT64 field outputs
        fields = [
            { address=0, name="voltage",      type="INT16",   scale=0.1   },
            { address=1, name="current",      type="INT32",   scale=0.001 },
            { address=3, name="power",        type="UINT32",  omit=true   },
            { address=5, name="energy",       type="FLOAT32", scale=0.001, measurement="W" },
            { address=7, name="frequency",    type="UINT32",  scale=0.1   },
            { address=9, name="power_factor", type="INT64",   scale=0.01  },
        ]

        [inputs.modbus.request.tags]
            machine = "impresser"
            location = "main building"

    [[inputs.modbus.request]]
        slave_id = 1
        byte_order = "ABCD"
        register = "input"

        ## Input example with type conversions
        fields = [
            { address=0, name="rpm",         type="INT16"                   },  # will result in INT64 field
            { address=1, name="temperature", type="INT16", scale=0.1        },  # will result in FLOAT64 field
            { address=2, name="force",       type="INT32", output="FLOAT64" },  # will result in FLOAT64 field
            { address=4, name="hours",       type="UINT32"                  },  # will result in UIN64 field
        ]

        [inputs.modbus.request.tags]
            machine = "impresser"
            location = "main building"
```

the sample of metrics for above configuration

```
> modbus,location=main\ building,machine=impresser,name=AHU_OT1,slave_id=1,type=coil motor1_overheating=true,motor1_run=0i 1713545273000000000
> motor,location=main\ building,machine=impresser,name=AHU_OT1,slave_id=1,type=coil jog=1i 1713545273000000000
> modbus,location=main\ building,machine=impresser,name=AHU_OT1,slave_id=1,type=holding_register current=2.222,frequency=555.5,power_factor=18763121947532330,voltage=111.10000000000001 1713545273000000000
> W,location=main\ building,machine=impresser,name=AHU_OT1,slave_id=1,type=holding_register energy=994.4440000000001 1713545273000000000
> modbus,location=main\ building,machine=impresser,name=AHU_OT1,slave_id=1,type=input_register force=262083463,hours=393157463i,rpm=1999i,temperature=299.90000000000003 1713545273000000000
```

### Step 3 - The Telegraf Command

[telegraf_service]: https://github.com/influxdata/telegraf/blob/master/docs/WINDOWS_SERVICE.md
[token]: https://github.com/influxdata/docs-v2/issues/493

1. [Running Telegraf as a Windows Service][telegraf_service]
2. [Document getting started on Windows][token]

validate the configuration, **_need to RUN the modbus simulator_**

```shell
> .\telegraf.exe --config "C:\Program Files\InfluxData\Telegraf\AHU_OT1.conf" --test
```

validate the configuration in another directory, _any name also can and end in .conf_

```shell
> .\telegraf.exe --config-directory "C:\Program Files\Telegraf\conf" --test

```

Telegraf can manage its own service through the --service flag:

| Command                            | Effect                        |
| ---------------------------------- | ----------------------------- |
| `telegraf.exe --service install`   | Install telegraf as a service |
| `telegraf.exe --service uninstall` | Remove the telegraf service   |
| `telegraf.exe --service start`     | Start the telegraf service    |
| `telegraf.exe --service stop`      | Stop the telegraf service     |

Run multiple Telegraf Windows Services

```shell
> .\telegraf.exe --config "C:\Program Files\InfluxData\Telegraf\AHU_OT1.conf" --service install --service-name telegraf-1 --service-display-name "Telegraf 1" --service-auto-restart
> .\telegraf.exe --config "C:\Program Files\InfluxData\Telegraf\AHU_OT2.conf" --service install --service-name telegraf-2 --service-display-name "Telegraf 2" --service-auto-restart
```

To uninstall Telegraf Windows Services

```shell
> .\telegraf.exe --service uninstall --service-name telegraf-1
> .\telegraf.exe --service uninstall
```
