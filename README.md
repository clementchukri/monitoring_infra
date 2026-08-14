# Infra monitoring - clusters de test

## Structure
- `clusters/kind-lab-tf-helm/` : cluster principal (Prometheus + Thanos Sidecar + Thanos Query)
- `clusters/kind-test/` : cluster secondaire (Prometheus + Thanos Sidecar uniquement, exposé en NodePort)

## Commandes de déploiement

### kind-lab-tf-helm

kubectl config use-context kind-lab-tf-helm

helm upgrade monitoring prometheus-community/kube-prometheus-stack
-n monitoring --version 88.2.0
-f clusters/kind-lab-tf-helm/kube-prometheus-stack/current-values.yaml
-f clusters/kind-lab-tf-helm/kube-prometheus-stack/thanos-sidecar-values.yaml

helm upgrade thanos-query oci://registry-1.docker.io/bitnamicharts/thanos
-n monitoring
-f clusters/kind-lab-tf-helm/thanos-query/thanos-query-values.yaml


### kind-test

kubectl config use-context kind-test

helm upgrade monitoring prometheus-community/kube-prometheus-stack
-n monitoring
-f clusters/kind-test/kube-prometheus-stack/prometheus-only-values.yaml


## Notes
- Sidecar kind-test exposé en NodePort sur 172.18.0.2:30901 (réseau Docker `kind`)
- Query sur kind-lab-tf-helm interroge les deux Sidecars (DNS discovery local + store statique distant)
