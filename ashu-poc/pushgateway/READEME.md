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
