# Lab 0. Installing Helm on IBM Cloud Kubernetes Service

The Helm client (`helm`) can be installed from source or pre-built binary releases. In this lab, we are going to use the pre-built binary release (Linux amd64) from the Helm community. Refer to the [Helm install docs](https://helm.sh/docs/intro/install/) for more details.

## Prerequisites

Create a Kubernetes cluster with [IBM Cloud Kubernetes Service](https://cloud.ibm.com/docs/containers/cs_tutorials.html#cs_cluster_tutorial), following the steps to also configure the IBM Cloud CLI with the Kubernetes Service plug-in.

## Installing the Helm Client (helm)

1. Download the [latest release of Helm v3](https://github.com/helm/helm/releases) for your environment, the steps below are for `Linux amd64`, adjust the examples as needed for your environment.

2. Unpack it: `$ tar -zxvf helm-v3.<x>.<y>-linux-amd64.tgz`.

3. Find the helm binary in the unpacked directory, and move it to its desired location: `mv linux-amd64/helm /usr/local/bin/helm`. It is best if the location you copy to is pathed, as it avoids having to path the helm commands.

4. The Helm client is now installed and can be tested with the command, `helm help`.

## Conclusion

You are now ready to start using Helm.
