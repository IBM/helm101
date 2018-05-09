# guestbook: an example chart for educational purpose.

This chart provides example of some of the important features of Helm.

The chart installs a [guestbook](https://github.com/IBM/guestbook/tree/master/v1) application.

## Installing the Chart

To install the chart with the default release name:

```bash
$ helm install charts/guestbook
```

To install the chart with your preference of release name, for example, `my-release`:

```bash
$ helm install --name my-release charts/guestbook
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
| `serviceType`              | Service type                                    | `LoadBalancer`                                             |

Specify each parameter using the `--set [key=value]` argument to `helm install`. For exampme,

```bash
$ helm install --set serviceType=NodePort charts/guestbook
```
