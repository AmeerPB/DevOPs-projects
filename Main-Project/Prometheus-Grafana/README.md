## steps to configure Prometheus-Grafana on K8s


### Reference 
[Techno Tim YT video](https://www.youtube.com/watch?v=fzny5uUaAeY)

### YT Video  Notes
[Techno Tim Video Notes](https://technotim.live/posts/kube-grafana-prometheus/)






> [!NOTE]
> Steps - Working condition

Official HELM [instalation page](https://helm.sh/docs/intro/install/)

```
curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
chmod 700 get_helm.sh
./get_helm.sh
helm version
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
kubectl create namespace monitoring

echo -n 'adminuser' > ./admin-user
echo -n 'p@ssword!' > ./admin-password


kubectl create secret generic grafana-admin-credentials --from-file=./admin-user --from-file=admin-password -n monitoring
kubectl describe secret -n monitoring grafana-admin-credentials

kubectl get secret -n monitoring grafana-admin-credentials -o jsonpath="{.data.admin-user}" | base64 --decode
kubectl get secret -n monitoring grafana-admin-credentials -o jsonpath="{.data.admin-password}" | base64 --decode


nano values.yaml

```

paste in values from [here](https://github.com/techno-tim/launchpad/blob/master/kubernetes/kube-prometheus-stack/values.yml)


> [!NOTE]
> Change the endpoints and add the internal IP of the EC2 instance


```

helm install -n monitoring prometheus prometheus-community/kube-prometheus-stack -f values.yaml --debug

kubectl get all -n monitoring


```


> [!NOTE]
> To access Grafana and Prometheus externally, edit their services to use LoadBalancer or NodePort

```
kubectl edit service grafana -n monitoring
kubectl edit service prometheus-prometheus -n monitoring
```

change the `type: ClusterIP` to `type: NodePort`










### Using HELM chart's with custom values.yml file as input

[values.yml file](https://github.com/techno-tim/launchpad/tree/master/kubernetes/kube-prometheus-stack)

### Helm repo command
`helm repo add prometheus-community https://prometheus-community.github.io/helm-charts`


### Installation notes

use "--debug" when running the below command 


`helm install -n monitoring prometheus prometheus-community/kube-prometheus-stack -f values.yaml`





> [!CAUTION]
> Steps for adding 2nd scrape job for Node Exporter
> 1. Add the additionalScrapeConfigs to the prometheus.prometheusSpec section in your values.yaml file:
> 
>     additionalScrapeConfigs:
>     - job_name: "worker_node"
>       static_configs:
>         - targets: ["52.14.138.83:9090"]
> 2. Create a ConfigMap for Additional Scrape Configurations
>    additional-scrape-configs.yaml
> 3. Apply the ConfigMap:
>    kubectl apply -f additional-scrape-configs.yaml
> 4. Reference the ConfigMap in the values.yaml file
>     additionalScrapeConfigs:
>       name: prometheus-additional-scrape-configs
>       key: additional-scrape-configs.yaml
> 5. Apply the Helm Upgrade
>    helm upgrade prometheus stable/prometheus-operator -f values.yaml -n monitoring
> 











