# steps to configure Prometheus-Grafana on K8s


## Using HELM chart's with custom values.yml file as input

values.yml file locaation: 
https://github.com/techno-tim/launchpad/tree/master/kubernetes/kube-prometheus-stack

## Helm repo command
**helm repo add prometheus-community https://prometheus-community.github.io/helm-charts


# Installation notes

use "--debug" when running the below command 


**helm install -n monitoring prometheus prometheus-community/kube-prometheus-stack -f values.yaml



# Reference 
Techno Tim YT --> https://www.youtube.com/watch?v=fzny5uUaAeY

# YT Video  Notes
https://technotim.live/posts/kube-grafana-prometheus/






