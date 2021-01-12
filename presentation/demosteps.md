# Demo steps

Provide steps to deploy, upgrade and rollback the guestbook chart.

## guestbook chart deployment

Check existing installation of Helm chart:

```bash
helm ls
```

Check what repo do you have:

```bash
helm repo list
```

Add repo:

```bash
helm repo add helm101 https://ibm.github.io/helm101/
```

Verify that helm101/guestbook is now in your repo:

```bash
helm repo list
helm search helm101
```

Deploy:

```bash
helm install helm101/guestbook --name myguestbook --set service.type=NodePort
```

Follow the output instructions to see your guestbook application.

*Note: For chart v0.1.0, the service type is set as `--set serviceType=NodePort`.*

Verify that your guestbook chart is deployed:

```bash
helm ls
```

Check chart release history:

```bash
helm history myguestbook
```

## guestbook chart deployment upgrade

Upgrade the deployment:

```bash
helm upgrade myguestbook helm101/guestbook
```

Check the history:

```bash
helm history myguestbook
```

## guestbook chart deployment rollback

Rollback to release 1:

```bash
helm rollback myguestbook 1
```

Check the history:

```bash
helm history myguestbook
```
