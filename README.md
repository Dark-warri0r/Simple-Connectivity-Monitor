## Monitoring Failed Pings with Grafana, InfluxDB, and Telegraf
This repository provides a comprehensive setup for monitoring failed ping requests using Grafana, InfluxDB, and Telegraf. The solution enables users to track network health and performance by visualizing ping results and identifying potential connectivity issues in real-time.

### Install Grafana

#### For Ubuntu/Debian:

```bash
sudo apt-get install software-properties-common
sudo add-apt-repository "deb https://packages.grafana.com/oss/deb stable main"
sudo wget -q -O - https://packages.grafana.com/gpg.key | sudo apt-key add -
sudo apt-get update
sudo apt-get install grafana
sudo systemctl start grafana-server
sudo systemctl enable grafana-server
```

### Install InfluxDB

#### For Ubuntu/Debian:

```bash
sudo apt update
sudo apt install influxdb
sudo service influxdb start
```
#### For other systems, refer to the InfluxDB installation documentation: [InfluxDB Installation](https://docs.influxdata.com/influxdb/v1.8/introduction/install/)


### Step 3: Install Telegraf

#### For Ubuntu/Debian:

```bash
sudo apt update
sudo apt install telegraf
```



## Configuration

- login into InfluxDB version 2x
- login with default password and username.
    - Username : admin
    - password : password

- create organization
- create bucket (database)
- open telegraf tab and create a telegraf module
- select created bucket
-	search for plugin named **ping**
-	click continue configuration
-	add description
-	edit the **input.ping** feild with following content:


~~~
  [[inputs.ping]]
  ## Hosts to send ping packets to.
  urls = ["ip_address"]
  timeout=2.0
  count = 4
  ~~~
 - click finish
  
 


- on Web UI of Infux open telegraf tab and  click on **set up instruction**
- click generate new API token
  - copy it and run in our machine
~~~
export INFLUX_TOKEN=<INFLUX_TOKEN>
~~~
copy the next command also to start telegrf and run it as service
<br />
**Example:**
~~~

telegraf --config http://ip_address:8086/api/v2/telegrafs/0c60d32df4c245400

~~~

### Configure Grafana

- Open Grafana in your browser (`http://localhost:3000`).
- Log in with the default credentials
  - Username : admin
  - password : admin
    
-Change the admin password and configure a data source:

   - Click on the gear icon (Settings) -> Data Sources.
   - Add a new data source of type InfluxDB.
   - Configure InfluxDB details (URL, database, user, password).
   - Click "Save & Test."

### Step 8: Create Grafana Dashboard and Panel

1. Create a new dashboard.
2. Add a new panel using the "Graph/Time series" panel type.
3. In the panel settings, choose the InfluxDB data source.
4. Write a query to visualize the ping metrics and identify failed pings.

For example:
~~~

from(bucket: "telegraf_db_name")
  |> range(start: v.timeRangeStart, stop: v.timeRangeStop)
  |> filter(fn: (r) => r["_measurement"] == "measurement")
  |> filter(fn: (r) => r["_field"] == "rFeild_name")
  |> filter(fn: (r) => r["host"] == "Device_name")
  |> aggregateWindow(every: v.windowPeriod, fn: mean, createEmpty: false)
  |> yield(name: "mean")
  
~~~ 
  
  
  
  

### Set Up Alerts in Grafana

- In the panel settings, go to the "Alert" tab.
- Enable the alert.
- Configure the conditions for the alert. Set "When" to `IS ABOVE` a value indicating a failed ping.
- Specify the details of the alert notification.

