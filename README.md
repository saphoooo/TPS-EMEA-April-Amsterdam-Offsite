# Use the Metrics Provider on Minikube

In this workshop, you'll install a 2 nodes Minikube cluster in order to run and scale an Apache server

## Set up the environement

For this workshop, there is a few requierements:

1. [Install `minikube`](https://minikube.sigs.k8s.io/docs/start/)
1. run a 2 nodes cluster
    ```
    $ minikube start -p datadog -n 2 --memory=16g --cpus=4
    ```
1. [Install `kubectl`](https://kubernetes.io/docs/tasks/tools/install-kubectl-macos/)
    > All Datadog laptops should come with `kubectl` already installed
1. [Install `helm`](https://helm.sh/docs/intro/install/#through-package-managers)

## Configure values.yaml

_You can find a preconfigured Datadog `values.yaml` file for Minikube [here](https://raw.githubusercontent.com/datadog-professional-services/Global-PS-Offsite-NYC/main/stef/demo-material/values.yaml?token=GHSAT0AAAAAABSTJKGNPUMM5PUGOES7QTI4YSMML3Q) if you don't want to spend hours to find the specific parameters that allow you to monitor you Kubernetes cluster properly_

### Provide you APP Key

The APP Key is mandatory for the metrics provider. You may update your `values.yaml` file with the APP Key, or on the fly, when executing the `helm` command:

- line ~30
    ```yaml
    # datadog.appKey -- Datadog APP key required to use metricsProvider
    ## If you are using clusterAgent.metricsProvider.enabled = true, you must set
    ## a Datadog application key for read access to your metrics.
    appKey: <your_app_key>
    ```

### Enable the metrics provider

- line ~582
    ```yaml
  # Enable the metricsProvider to be able to scale based on metrics in Datadog
  metricsProvider:
    # clusterAgent.metricsProvider.enabled -- Set this to true to enable Metrics Provider
    enabled: true
    ```

_ref [Datadog Docs - Custom Metrics Server](https://docs.datadoghq.com/agent/cluster_agent/external_metrics/?tab=helm)_

## Install Datadog Agent

```bash
$ helm repo add datadog https://helm.datadoghq.com
$ helm repo add stable https://charts.helm.sh/stable
$ helm repo update
$ helm install datadog -f values.yaml --set datadog.apiKey='<your_api_key>' \
  --set datadog.appKey='<your_app_key>' datadog/datadog
```

## Set up Apache

_You can find preconfigured `values.yaml`, `hpa-apache.yaml` and `apache-config.yaml` files [here](https://github.com/datadog-professional-services/Global-PS-Offsite-NYC/tree/main/stef/apache), or for Kubernetes and Datadog fellows, try to enable the integration by yourself_

```bash
$ helm repo add bitnami https://charts.bitnami.com/bitnami
$ helm repo update
$ kubectl apply -f apache-config.yaml
$ helm install apache -f values.yaml bitnami/apache
```

## Enable Apache integration in Datadog

Do you really think I'll provide you with guided steps here?

### Choose the metric to scale

Pick up on metric of interest collected by the integration

- line 16
    ```yaml
          metric:
            name: <CHOOSE_THE_BEST_METRIC_TO_SCALE>
    ```

### Create the horizontal pod autoscaler

```bash
$ kubectl apply -f hpa-apache.yaml
```

## Generate trafic on your server

1. Retrieve the external port on witch Apache is listening
1. Generate trafic with `curl` or Apache Bench `ab` (those tools are already on your machine)
1. Generate enough trafic to see you server scaling out

## Additional activity

Create a dashboard in Datadog to understand what happens during the scale out
