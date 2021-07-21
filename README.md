# Fluentd container for Elasticsearch and Influxdb 

## Overview

This container exposes a 'forward' input for Fluentbit, and sends data to ElasticSearch and Influxdb for storage. It's ideal for deployment with Docker or Kubernetes where multiple clients will be sending logs & metrics through a buffered output. 

During operation, any data tagged with `metrics` will be routed to Influxdb, and all other data will be routed to ElasticSearch. `events` will be routed to the `events-yyyy.mm.dd` index, while all other logs will be submitted to the `syslog-yyyy.mm.dd` index. 

This container will also apply some basic postprocessing by performing GeoIP lookups on any public IPs in the logs. 

## Usage

All config files are included in the image. Only basic information is needed to startup and run the container. 

### Environment variables

Variable | Example
-------- | -----
`INFLUXDB_HOST` | `influx.default.svc.cluster.local:8086`
`INFLUXDB_TOKEN` | `xxxxxxx`
`ELASTICSEARCH_HOST` | `es.default.svc.cluster.local:9200`

### Client setup 

The container exposes an unencrypted Forward input on port 5000/tcp. An example configuration for Fluentbit would be an output sending data to this server's IP: 

```ini
[OUTPUT]
    Name        forward
    Match       *
    Host        dataserver.onetwoseven.one
    Port        5000
    net.keepalive on
    net.keepalive_idle_timeout 30
    Retry_Limit false
```
