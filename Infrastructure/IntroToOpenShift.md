# Getting started with OpenShift 4

## 1. Overview
  - Pluralsight course: "Getting Started with OpenShift 4" by Ben Weissman

## 2. Container, Kubernetes and OpenShift Basics
  - Container vs Virtual machine: less independent, but less resource duplication when run multiple container
    + Pros: Fast & consistent deployments, ease of patching (i.e by update source image)
  - Kubernetes: orchestrating container via code, using Pod, which is completed application from one/multiple containers
  - `kubectl`: command-line tools to communicate with Kubernetes cluster
  - Kubernetes resource allocation
    + Request: minimun requirement --> Pod will not start if request is higher than available resources
    + Limit: maximum resource
  - Persistent Volume (PV) / Persistent Volume Claims (PVC): allows data storage
    + accessing mode: allow sharing of volume to other Kubernetes Pod
    + allocation: either static via config file, or dynamic with provisioners, which dynamically create & claim storage
  - Networking
    + ClusterIP: basically an internal IP & port --> for accessing within Kubernetes Cluster
    + NodePort: port in the 30K range --> can be access from outside the cluster to a specific node within the cluster
    + LoadBalancer: have its own IP & port
  - OpenShift: ==> Kubernetes for Enterprise
    + Have 3 control planes
    + More tools: OpenShift CLI, OpenShift Developer CLI and OpenShift Web Console
    + Come with predefined pods: Grafana (monitoring/metrics), Prometheus, Cri-o, Tekton, etc.

## 3. Deploying OpenShift

## 4. Accessing and working with OpenShift
