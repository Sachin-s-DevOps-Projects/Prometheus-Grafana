# Install using Helm

## Add helm repo

`helm repo add grafana https://grafana.github.io/helm-charts`

## Update helm repo

`helm repo update`

## Install helm 

`helm install grafana grafana/grafana`

## Expose Grafana Service

` kubectl expose service grafana --type=NodePort --target-port=3000 --name=grafana-ext`

### To import dashboard
`3662`
>Using this id, we will be able to pre-existing (pre-created) template. This is the standard template that would get a lot of information from your kubernetes cluster. This template would run queries

## To get more information

* To get more information, we have to expose kube-state-metrics (prometheus-kube-state-metrics) service 
* kube-state-metrics is ClusterIP type service , so we have to convert it into nodePort service, then we can get more metrics details on that specific endpoint  which we take from exposing.
