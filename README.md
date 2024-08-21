# Deploying the WSO2 integration observability solution

## Pre-requisites
- Kubernetes cluster
- Helm

## Deployment

1. Clone this repository to a local folder. Local repository path is shown as `<repo-path>` in remaining sections.

2. Create a Kubernetes namespace named "observability"
```
kubectl create ns observability
```

3. Deploy the OpenSearch operator using the following commands:
```
helm repo add opensearch-operator https://opensearch-project.github.io/opensearch-k8s-operator/
helm repo update
helm install opensearch-operator opensearch-operator/opensearch-operator -n observability --version 2.6.1
```

4. Deploy the OpenSearch cluster using the below command: 
```
kubectl apply -f <repo-path>/observability/deployment/opensearch.yaml
``` 
It will take few minutes to deploy all components of the cluster. Deployment staus can be monitored using the `kubectl get pods -n observability` command. All opensearch pods except the *opensearch-securityconfig* will be in *Ready* state once the deployment is complete.

5. Configure a port forwarding to the Open Search dashboard.
```
kubectl port-forward svc/opensearch-dashboards 5601:5601 -n observability
```

6. Deploy the fluent-bit agent using the following commands:
```
helm repo add fluent https://fluent.github.io/helm-charts
helm repo update
helm install fluent-bit fluent/fluent-bit --version 0.43.0 -n observability -f <repo-path>/observability/deployment/fluent-bit.yaml
```

7. Follow the following steps to deploy integration dashboards:
    1. Log in to the OpenSearch dashboard at URL [http://localhost:5601](http://localhost:5601) using the default credentials *(username: admin, password: admin)*
    2. Navigate to *Dashboard management -> Saved objects* menu
    3. Click *Import* link and import the `<repo-path>/observability/dashboards/integration-dashboards.ndjson` file.
    4. Navigate to *Dashbaords* menu and you will see two dashbaords for WSO2 Micro Integrator and Ballerina.
