# Introduction to Helm v2

Helm is a tool that streamlines installation and management of Kubernetes applications. Think of Helm as an apt/yum/homebrew
for Kubernetes. This repository provides learning materials for Helm.

The branch of this repo that you are using is `helm-v2` and it uses the [Helm v2](https://v2.helm.sh/) release. If you
are using Helm v3 then go to the default [master](https://github.com/IBM/helm101) branch. Please refer to [Helm Status](#helm-status)
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
 
