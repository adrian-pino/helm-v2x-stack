# V2X Helm Charts & Local Helm Chart Repo (ChartMuseum)
Repository containing the two Helm Charts that form the V2X Stack: MQTT Broker & V2XCom. The V2X Stack contains a MQTT Broker and as many V2XCom as necessary. This can be achieved running the V2XCom Helm Chart with different configurations specifying info such as "RSU-id", "IP", "VLAN tag", etc. 

## Lead developers
- Adrian Pino  Martínez (adrian.pino@i2cat.net)

## Repo structure
```
.
├── mqtt-broker
│   ├── Chart.yaml
│   ├── charts
│   ├── readme.txt
│   ├── templates
│   │   ├── _helpers.tpl
│   │   ├── mqtt-deployment.yaml
│   │   └── mqtt-service.yaml
│   └── values.yaml
└── v2xcom
    ├── Chart.yaml
    ├── charts
    ├── readme.txt
    ├── templates
    │   ├── _helpers.tpl
    │   └── v2xcom-deployment.yaml
    └── values.yaml
```

Below there are list the necessary commands to push Helm Charts to a local helm chart repo (ChartMuseum) in order to use it with OSM.

## Launch Chart museum
```
chartmuseum --debug --port=8080   --storage="local"   --storage-local-rootdir="./chartstorage"
```

## Package the Helm Chart (.tgz format)
```
helm package .
```

At this very moment we're able to push our helm-charts to chart museum.

## Push Helm Chart to the repo
```
curl --data-binary "@./mqtt-broker/mqtt-broker-0.1.0.tgz" http://192.168.123.38:8080/api/charts
curl --data-binary "@./v2xcom/v2xcom-0.1.0.tgz" http://192.168.123.38:8080/api/charts
```

## Check that the helm chart has been added to chart museum
```
cat ~/helm/chartstorage/index-cache.yaml
```

## Also we could get the Helm Charts uploaded to the repo via curl
```
curl  http://192.168.123.38:8080/api/charts | jq
```

## To delete a helm-chart from chart museum
```
curl -X DELETE http://192.168.123.38:8080/api/charts/mqtt-broker/0.1.0
curl -X DELETE http://192.168.123.38:8080/api/charts/v2xcom/0.1.0
```

## Now update the repos inside OSM's LCM pod
```
kubectl -n osm exec -it <LCM pod> -- sh -c "helm repo update"

#Example:
kubectl -n osm exec -it lcm-864ccc87c6-wn6wv -- sh -c "helm repo update"
```
