# monitoring
Figuring out how to monitor boxes in the field.

## InfluxDB

I signed up for the Amazon Marketplace version of hosted InfluxDB - went to the integrations:

```
pip install influxdb-client
pip install flightsql-dbapi
pip instal pandas
```


```
export INFLUXDB_TOKEN=jLhXXXOhy99JlT6d2ip6b_68gtVdJvTBuqvbpQWYjZqqIZSs6x4kMFewrYrHc_VAECNrCGawMLtA1WPV50JodA==
```

```python
import influxdb_client, os, time
from influxdb_client import InfluxDBClient, Point, WritePrecision
from influxdb_client.client.write_api import SYNCHRONOUS

token = os.environ.get("INFLUXDB_TOKEN")
org = "drone"
url = "https://us-east-1-1.aws.cloud2.influxdata.com"

write_client = influxdb_client.InfluxDBClient(url=url, token=token, org=org)

#...

bucket="node-monitoring"

# Define the write api
write_api = write_client.write_api(write_options=SYNCHRONOUS)

data = {
  "point1": {
    "location": "Klamath",
    "species": "bees",
    "count": 23,
  },
  "point2": {
    "location": "Portland",
    "species": "ants",
    "count": 30,
  },
  "point3": {
    "location": "Klamath",
    "species": "bees",
    "count": 28,
  },
  "point4": {
    "location": "Portland",
    "species": "ants",
    "count": 32,
  },
  "point5": {
    "location": "Klamath",
    "species": "bees",
    "count": 29,
  },
  "point6": {
    "location": "Portland",
    "species": "ants",
    "count": 40,
  },
}

for key in data:
  point = (
    Point("census")
    .tag("location", data[key]["location"])
    .field(data[key]["species"], data[key]["count"])
  )
  write_api.write(bucket=bucket, org=org, record=point)
  time.sleep(1) # separate points by 1 second

print("Complete. Return to the InfluxDB UI.")

```

