# guestbook: an example chart for educational purpose.

This chart provides example of some of the important features of Helm.

The chart installs a [guestbook](https://github.com/IBM/guestbook/tree/master/v1) application.

## Installing the Chart

Add the repository to your local environment:
```bash
$ helm repo add my-repo https://ibm.github.io/helm101/
```

To install the chart with the default release name:

```bash
$ helm install my-repo/guestbook
```

To install the chart with your preference of release name, for example, `my-release`:

```bash
$ helm install my-repo/guestbook --name my-release
```

### Uninstalling the Chart

To completely uninstall/delete the `my-release` deployment:

```bash
$ helm delete --purge my-release
```

The command removes all the Kubernetes components associated with the chart and deletes the release.

## Configuration

The following tables lists the configurable parameters of the chart and their default values.

| Parameter                  | Description                                     | Default                                                    |
| -----------------------    | ---------------------------------------------   | ---------------------------------------------------------- |
| `image.repository`         | Image repository                                | `ibmcom/guestbook`                                         |
| `image.tag`                | Image tag                                       | `v1`                                                       |
| `image.pullPolicy`         | Image pull policy                               | `Always`                                                   |
| `service.type`             | Service type                                    | `LoadBalancer`                                             |
| `service.port`             | Service port                                    | `3000`                                                     |
| `redis.slaveEnabled`       | Redis slave enabled                             | `true`                                                     |
| `redis.port`               | Redis port                                      | `6379`                                                     |

Specify each parameter using the `--set [key=value]` argument to `helm install`. For example,

```bash
$ helm install my-repo/guestbook --set service.type=NodePort
```
