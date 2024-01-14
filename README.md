# apisix-with-dashboard
## Overview
This repository contains a docker-compose file to deploy Apache APISIX with dashboard.

## Getting Started
#### To deploy Apache APISIX using Docker, run the following command:
Create a folder named `etcd_data` in configs folder, and then run the following command:
```bash
docker-compose up -d
```

## Configure Routes
#### Create a route with api key
```bash
curl -i "http://127.0.0.1:9180/apisix/admin/routes" -H 'X-API-KEY: edd1c9f034335f136f87ad84b625c8f1' -X PUT -d '
{
  "id": "getting-started-ip",
  "uri": "/ip",
  "upstream": {
    "type": "roundrobin",
    "nodes": {
      "httpbin.org:80": 1
    }
  }
}'
```

#### Validate the route
```bash
curl "http://127.0.0.1:9080/ip"
```
You should get the following response:
```bash
{
  "origin": "172.18.0.1, 103.155.219.47"
}
```

## Testing Load Balancing
#### Create another route with api key
```bash
curl -i "http://127.0.0.1:9180/apisix/admin/routes" -H 'X-API-KEY: edd1c9f034335f136f87ad84b625c8f1' -X PUT -d '
{
  "id": "getting-started-headers",
  "uri": "/headers",
  "upstream" : {
    "type": "roundrobin",
    "nodes": {
      "httpbin.org:443": 1,
      "mock.api7.ai:443": 1
    },
    "pass_host": "node",
    "scheme": "https"
  }
}'
```

#### Validate the upstream
```bash
hc=$(seq 100 | xargs -I {} curl "http://127.0.0.1:9080/headers" -sL | grep "httpbin" | wc -l); echo httpbin.org: $hc, mock.api7.ai: $((100 - $hc))
```
The result shows that the traffic is distributed to both upstreams:
```bash
httpbin.org: 49, mock.api7.ai: 51
```

## APISIX Documentation
For more information, refer to [Getting started with Apache APISIX Documentation](https://apisix.apache.org/docs/apisix/getting-started/README/)