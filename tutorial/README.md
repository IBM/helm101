# Why Helm?

[Helm](https://v2.helm.sh/) is often described as the Kubernetes application package manager. So, what does Helm give you over using kubectl directly?

# Objectives

These labs provide an insight on the advantages of using Helm 2 over using Kubernetes directly through `kubectl`. When you complete all the labs, you'll:
* Understand the core concepts of Helm
* Understand the advantages of deployment using Helm over Kubernetes directly, looking at:
  * Application management
  * Updates
  * Configuration
  * Revision management
  * Repositories and chart sharing

# Helm Status

Refer to [Helm Status](../README.md#helm-status) for more details on Helm releases.

# Prerequisites

* Have a running Kubernetes cluster. See the [IBM Cloud Kubernetes Service](https://cloud.ibm.com/docs/containers/cs_tutorials.html#cs_cluster_tutorial) or [Kubernetes Getting Started Guide](https://kubernetes.io/docs/setup/) for details about creating a cluster.
* Have Helm installed and initialized with the Kubernetes cluster. See [Installing Helm on IBM Cloud Kubernetes Service](Lab0/README.md) or the [Helm Quickstart Guide](https://v2.helm.sh/docs/using_helm/#quickstart) for getting started with Helm.

# Helm Overview

Helm is a tool that streamlines installation and management of Kubernetes applications. It uses a packaging format called "charts", which are a collection of files that describe Kubernetes resources. It can run anywhere (laptop, CI/CD, etc.) and is available for various operating systems, like OSX, Linux and Windows.

![helm-architecture](images/helm-architecture.png)

Helm 2 has a client-server architecture (Helm 3 has a [client-side architecture](https://github.com/IBM/helm101/tree/master/tutorial#helm-overview)) with the client called `helm` and the server called `Tiller`. The client is a CLI which users interact with to perform different operations like install/upgrade/delete etc. The client interacts with Tiller and the chart repository. Tiller interacts with the Kubernetes API server. It renders Helm template files into Kubernetes manifest files which it uses to perform operations on the Kubernetes cluster via the Kubernetes API. See the [Helm Architecture](https://v2.helm.sh/docs/architecture/) for more details. 

A [chart](https://v2.helm.sh/docs/developing_charts) is organized as a collection of files inside of a directory where the directory name is the name of the chart. It contains template YAML files which facilitates providing configuration values at runtime and eliminates the need of modifying YAML files. These templates provide programming logic as they are based on the [Go template language](https://golang.org/pkg/text/template/), functions from the [Sprig lib](https://github.com/Masterminds/sprig) and other [specialized functions](https://v2.helm.sh/docs/developing_charts/#chart-development-tips-and-tricks).

The chart repository is a location where packaged charts can be stored and shared. This is akin to the image repository in Docker. Refer to [The Chart Repository Guide](https://v2.helm.sh/docs/developing_charts/#the-chart-repository-guide) for more details.

# Helm Abstractions

Helm terms :
* Chart - It contains all of the resource definitions necessary to run an application, tool, or service inside of a Kubernetes cluster. A chart is basically a package of pre-configured Kubernetes resources.
* Config - Contains configuration information that can be merged into a packaged chart to create a releasable object.
* helm - Helm client. Communicates to Tiller through the Helm API - [HAPI](https://v2.helm.sh/docs/developers/#the-helm-api-hapi) which uses [gRPC](https://grpc.io/).
* Release - An instance of a chart running in a Kubernetes cluster.
* Repository - Place where charts reside and can be shared with others.
* Tiller - Helm server. It interacts directly with the [Kubernetes API](https://kubernetes.io/docs/concepts/overview/kubernetes-api/) server to install, upgrade, query, and remove Kubernetes resources.

To get started, head on over to [Lab 1](Lab1/README.md). 
