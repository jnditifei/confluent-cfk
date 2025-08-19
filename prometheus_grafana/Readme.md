### Adding helm repos
```
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts

helm repo update
```
### Installing prometheus and grafana
```
helm install prom prometheus-community/kube-prometheus-stack -f ./values.yaml
```
```
kubectl label namespace confluent monitoring=confluent
```
### Build CP customs images with JMX exporter for prometheus
```
docker build -t cp_server_jmx .
```
### Install podmonitor resource
```
kubectl apply -f pm-confluent.yaml -n confluent
```
### Create configmaps for grafana dashboards
```
kubectl create configmap grafana-zookeeper-cluster --from-file=dashboards/zookeeper-cluster.json

kubectl label configmap grafana-zookeeper-cluster grafana_dashboard=1
```

```
kubectl create configmap grafana-kafka-cluster --from-file=dashboards/kafka-cluster.json

kubectl label configmap grafana-kafka-cluster grafana_dashboard=1
```
### Forward ports
```
kubectl port-forward svc/prometheus-operated  9090:9090 -n default > /dev/null 2>&1 &
kubectl port-forward svc/prom-grafana 3000:80 -n default > /dev/null 2>&1 &
```
