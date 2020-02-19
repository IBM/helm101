# Lab 0. Installing Helm on IBM Cloud Kubernetes Service

Tiller was the Helm server component before Helm v3 and was removed since Helm v3.

Helm can be installed from source or pre-built binary releases. In this lab, we are going to use the pre-built binary release (Linux amd64) from the Helm community. Refer to the [Helm install docs](https://docs.helm.sh/using_helm/#install-helm) for more details. 

# Prerequisites

Create a Kubernetes cluster with [IBM Cloud Kubernetes Service](https://cloud.ibm.com/docs/containers/cs_tutorials.html#cs_cluster_tutorial), following the steps to also configure the IBM Cloud CLI with the Kubernetes Service plug-in.

# Installing the Helm CLI

1. Download the [latest release Helm](https://github.com/helm/helm/releases) for your environment, which in this case is `Linux amd64`.

2. Unpack it: `$ tar -zxvf helm-v2.<x>.<y>-linux-amd64.tgz`.

3. Find the helm binary in the unpacked directory, and move it to its desired location: `mv linux-amd64/helm /usr/local/bin/helm`. It is best if the location you copy to is pathed, as it avoids having to path the helm commands.

4. The Helm CLI is now installed and can be tested with the command, `helm help`. Refer to the doc [installing Client](https://docs.helm.sh/using_helm/#installing-the-helm-client) for more details.

# Conclusion

You are now ready to start using Helm.
