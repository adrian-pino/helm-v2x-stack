# Helm Charts Implementing the V2X Stack
Repository containing the two Helm Charts that form the V2X Stack: MQTT Broker & V2XCom. The V2X Stack contains a MQTT Broker and as many V2XCom as necessary. This can be achieved running the V2XCom Helm Chart with different configurations specifying info such as "RSU-id", "IP", "VLAN tag", etc. 

## Lead developers
- Adrian Pino  Martínez (adrian.pino@i2cat.net)

## Repo structure
```
.
├── mqtt-broker
│   ├── Chart.yaml
│   ├── readme.txt
│   ├── templates
│   │   ├── mqtt-deployment.yaml
│   │   └── mqtt-service.yaml
│   └── values.yaml
└── v2xcom
    ├── Chart.yaml
    ├── readme.txt
    ├── templates
    │   └── v2xcom-deployment.yaml
    └── values.yaml
```

Below there are list the necessary commands to push Helm Charts to a local helm chart repo (ChartMuseum) in order to use it with OSM.

## Launch Chart museum
```
chartmuseum --debug --port=8080   --storage="local"   --storage-local-rootdir="./chartstorage"
```

## Package a Helm Chart (.tgz format)
```
helm package .
```

## Upload a Helm Chart
```
curl --data-binary "@./mqtt-broker/mqtt-broker-0.1.0.tgz" http://192.168.123.38:8080/api/charts
curl --data-binary "@./v2xcom/v2xcom-0.1.0.tgz" http://192.168.123.38:8080/api/charts
```

## Helm Chart upload validation
To check that the helm chart has been added to chart museum:
```
cat ~/helm/chartstorage/index-cache.yaml
```

We could check it directly reaching the ChartMuseum instance
```
curl  http://192.168.123.38:8080/api/charts | jq
```

## Delete a Helm  Chart
```
curl -X DELETE http://192.168.123.38:8080/api/charts/mqtt-broker/0.1.0
curl -X DELETE http://192.168.123.38:8080/api/charts/v2xcom/0.1.0
```

## ETSI OSM Integration. NFV Environments. CNF instantiation.
If a chart gets updated, the OSM's LCM pod should update its helm repositories to catch the later updates.
```
kubectl -n osm exec -it <LCM pod> -- sh -c "helm repo update"
```
