# Install using Helm

## Add helm repo

`helm repo add prometheus-community https://prometheus-community.github.io/helm-charts`

## Update helm repo

`helm repo update`
>This is good practice, we must do it whenever we are gonna install anything using helm

## Install helm 

`helm install prometheus prometheus-community/prometheus`

## Expose Prometheus Service

This is required to access prometheus-server using your browser. 

Because by default prometheus-server service will be a clusterIP type service, so we have to convert it into nodePort type service. Otherwise we won't be able to expose it (We can only use it in the cluster,  when we use clusterIp. 

`kubectl expose service prometheus-server --type=NodePort --target-port=9090 --name=prometheus-server-ext`

