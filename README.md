# Introduction to Helm

Helm is a tool that streamlines installation and management of Kubernetes applications. Think of Helm as an apt/yum/homebrew
for Kubernetes. This repository provides learning materials for Helm.

The default branch of this repo is `master` and it uses the current major release of [Helm](https://helm.sh/) which is v3. If you
are using Helm v2 then go to the [helm-v2](https://github.com/IBM/helm101/tree/helm-v2) branch. Please refer to [Helm Status](#helm-status)
for more details on the different Helm releases.

## Helm Status

[Helm v3 was released](https://helm.sh/blog/helm-3-released/) in November 2019. The interface is quite similar but there
are major changes to the architecture and internal plumbing of Helm, essentially making it a new product when compared forensically
against Helm 2. Check out [what’s in Helm 3](https://developer.ibm.com/technologies/containers/blogs/kubernetes-helm-3/) for more
details.

The [Helm 2 Support Plan](https://helm.sh/blog/2019-10-22-helm-2150-released/#helm-2-support-plan) documents a 1 year "maintenance
mode" for Helm v2. It states the following:

- 6 month bug fixes until May 13 2020
- 6 month security fixes until November 13 2020
- At 1 year on November 13 2020, support for Helm v2 will end
