## Installing pushgateway using helm chart

### adding repo if not present 
```
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
```
### Installing 

```
helm install my-prometheus-pushgateway prometheus-community/prometheus-pushgateway --version 2.4.1
```

### verify it 

```
prometheus-final-poc kubectl get po  | grep -i push 
my-prometheus-pushgateway-578d854f44-hr2c7                     1/1     Running   0          2m51s
➜  prometheus-final-poc 

```

=======>

```
prometheus-final-poc kubectl get svc  | grep -i push 
my-prometheus-pushgateway                           ClusterIP   10.100.71.172    <none>        9091/TCP                     3m12s
➜  prometheus-final-poc 
```

## Note: you can access dashboard of pushgateway 

### adding pushgw to prometheus scrape config 

### yaml of CRD scrapeconfig

```
  prometheus-final-poc cat pushgw-scrape-prom.yaml 
apiVersion: monitoring.coreos.com/v1alpha1
kind: ScrapeConfig
metadata:
  name: pushgw-scrape-config 
  namespace: monitoring
  labels: # this must label of prometheus api-resource under scrape config 
    release: my-kube-prometheus-stack
spec:
  staticConfigs:
    - labels:
        job: ashu-push-gateway
      targets:
        - my-prometheus-pushgateway.monitoring.svc.cluster.local:9091
```

### apply this 

```
 prometheus-final-poc kubectl apply -f pushgw-scrape-prom.yaml 
scrapeconfig.monitoring.coreos.com/pushgw-scrape-config created
➜  prometheus-final-poc kubectl get scrapeconfigs.monitoring.coreos.com 
NAME                      AGE
dashbiard-scrape-config   82m
pushgw-scrape-config      9s

```

### Note: you can check in prometheus UI under configuration 

## Sending data to pushgateway 
### Using curl 

```
 echo 'some_metric 3.14' | curl --data-binary @- http://localhost:8899/metrics/job/some_job/instance/some_instance
```

### like 

```
echo 'custom_metric 42' | curl --data-binary @- http://localhost:8899/metrics/job/webserver/instance/192.168.1.100
```

### python code 

```
from prometheus_client import CollectorRegistry, Gauge, push_to_gateway

registry = CollectorRegistry()
g = Gauge('job_last_success_unixtime', 'Last time a batch job successfully finished', registry=registry)
g.set_to_current_time()
push_to_gateway('localhost:8899', job='ashutime-check', registry=registry)
```

