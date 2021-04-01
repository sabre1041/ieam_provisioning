# IEAM Provisioning

Tooling to aid in the provisioning of IEAM

## Overview

Automation of IEAM is facilitated through the use [Ansible based tooling](https://github.com/sabre1041/ieam_ansible). The contents of this repository will provide the primitives to enable the overall automation.

## Cluster Deployment

The deployment of an IEAM to an edge OpenShift cluster can be achieved through the use of assets in the [cluster](cluster) directory.

### Prerequisites

* Kubernetes CLI (kubectl) or OpenShift Command Line (oc) tool
* Access to the cluster with `cluster-admin` permissions

### Configuration

A configuration file called [ieam_provisioning_vars.yaml](ieam_provisioning_vars.yaml) is used to configure the access to the Hub IEAM cluster and the specific configurations for the newly created edge cluster.

The following should be set based on variables from the Hub cluster:

```
ieam_cluster_url: "<ieam_cluster_url>"
ieam_api_key: "<ieam_api_key>"
ieam_organization: "<ieam_organization>"
```

Each edge cluster must be given a name. This is achieved by setting the `ieam_node_name` variable.

Finally, if there is a desired pattern to configure, it can be specified by setting the `ieam_pattern` variable.

### Provision

Setup the edge IEAM cluster by deploying resources on the edge cluster which will run Ansible automation to setup and register the fluster.

Ensure that you are currently logged into the cluster and run the following command:

```
kubectl apply -k cluster
```

Verify the status of the job that will register the edge cluster to the hub

```
kubectl -n openhorizon-agent logs -f $(kubectl get pods -n openhorizon-agent -l=job-name=ieam-provisioner -o jsonpath='{ .items[*].metadata.name }')
```

Verify the agent has been registered to the hub

```
kubectl -n openhorizon-agent exec -f $(kubectl get pods -n openhorizon-agent -l=app=agent -o jsonpath='{ .items[*].metadata.name }') hzn node list
```