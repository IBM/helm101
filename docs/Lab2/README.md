# Lab 2. Make changes with Helm

In [Lab 1](../Lab1/README.md), we installed the guestbook sample app by using Helm and saw the benefits over using `kubectl`. You probably think that you're done and know enough to use Helm. But what about updates or improvements to the chart? How do you update your running app to pick up these changes?

In this lab, we're going to look at how to update our running app when the chart has been changed. To demonstrate this, we're going to make changes to the original `guestbook` chart by:

* Removing the Redis slaves and using just the in-memory DB
* Changing the type from `LoadBalancer` to `NodePort`.

It seems contrived but the goal of this lab is to show you how to update your apps with Kubernetes and Helm. So, how easy is it to do this? Let's take a look below.

## Scenario 1: Update the application using `kubectl`

In this part of the lab we will update the previously deployed application [Guestbook](https://github.com/IBM/guestbook), using Kubernetes directly.

1. This is an **optional** step that is not technically required to update your running app. The reason for doing this step is "house keeping" - you want to have the correct files for the current configuration that you have deployed. This avoids making mistakes if you have future updates or even rollbacks. In this updated configuration, we remove the Redis slaves. To have the directory match the configuration, move/archive or simply remove the Redis slave files from the guestbook repo tree:

   ``` console
   cd guestbook/v1
   rm redis-slave-service.yaml
   rm redis-slave-deployment.yaml
   ```

   > Note: you can reclaim these files later with a `git checkout -- <filename>` command, if desired

1. Delete the Redis slave service and pods:

   ```console
   $ kubectl delete svc redis-slave --namespace default
   service "redis-slave" deleted
   $ kubectl delete deployment redis-slave --namespace default
   deployment.extensions "redis-slave" deleted
   ```

1. Update the guestbook service from `LoadBalancer` to `NodePort` type:

   ```console
   sed -i.bak 's/LoadBalancer/NodePort/g' guestbook-service.yaml
   ```

   > Note: you can reset the files later with a `git checkout -- <filename>` command, if desired

1. Delete the guestbook service:

   ```console
   kubectl delete svc guestbook --namespace default
   ```

1. Re-create the service with `NodePort` type:

   ```console
   kubectl create -f guestbook-service.yaml
   ```

1. Check the updates, using

   ```console
   kubectl get all --namespace default
   ```

   ```console
   $ kubectl get all --namespace default
   NAME                                READY     STATUS    RESTARTS   AGE
   pod/guestbook-v1-7fc76dc46-9r4s7    1/1       Running   0          1h
   pod/guestbook-v1-7fc76dc46-hspnk    1/1       Running   0          1h
   pod/guestbook-v1-7fc76dc46-sxzkt    1/1       Running   0          1h
   pod/redis-master-5d8b66464f-pvbl9   1/1       Running   0          1h

   NAME                   TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
   service/guestbook      NodePort    172.21.45.29    <none>        3000:31989/TCP   31s
   service/kubernetes     ClusterIP   172.21.0.1      <none>        443/TCP          9d
   service/redis-master   ClusterIP   172.21.232.61   <none>        6379/TCP         1h

   NAME                             READY   UP-TO-DATE   AVAILABLE   AGE
   deployment.apps/guestbook-demo   3/3     3            3           1h
   deployment.apps/redis-master     1/1     1            1           1h

   NAME                                      DESIRED   CURRENT   READY     AGE
   replicaset.apps/guestbook-v1-7fc76dc46    3         3         3         1h
   replicaset.apps/redis-master-5d8b66464f   1         1         1         1h
   ```

   > Note: The service type has changed (to `NodePort`) and a new port has been allocated (`31989` in this output case) to the guestbook service. All `redis-slave` resources have been removed.

1. View the guestbook

   Get the public IP of one of your nodes:

   ```console
   kubectl get nodes -o wide
   ```

   Navigate to the IP address plus the node port that printed earlier.

## Scenario 2: Update the application using Helm

In this section, we'll update the previously deployed `guestbook-demo` application by using Helm.

Before we start, let's take a few minutes to see how Helm simplifies the process compared to using Kubernetes directly. Helm's use of a [template language](https://helm.sh/docs/chart_template_guide/getting_started/) provides great flexibility and power to chart authors, which removes the complexity to the chart user. In the guestbook example, we'll use the following capabilities of templating:

* Values: An object that provides access to the values passed into the chart. An example of this is in `guestbook-service`, which contains the line `type: {{ .Values.service.type }}`. This line provides the capability to set the service type during an upgrade or install.
* Control structures: Also called “actions” in template parlance, control structures provide the template author with the ability to control the flow of a template’s generation. An example of this is in `redis-slave-service`, which contains the line `{{- if .Values.redis.slaveEnabled -}}`. This line allows us to enable/disable the REDIS master/slave during an upgrade or install.

The complete `redis-slave-service.yaml` file shown below, demonstrates how the file becomes redundant when the `slaveEnabled` flag is disabled and also how the port value is set. There are more examples of templating functionality in the other chart files.

```yaml
{{- if .Values.redis.slaveEnabled -}}
apiVersion: v1
kind: Service
metadata:
  name: redis-slave
  labels:
    app: redis
    role: slave
spec:
  ports:
  - port: {{ .Values.redis.port }}
    targetPort: redis-server
  selector:
    app: redis
    role: slave
{{- end }}
```

Enough talking about the theory. Now let's give it a go!

1. First, lets check the app we deployed in Lab 1 with Helm. This can be done by checking the Helm releases:

   ```console
   helm list -n helm-demo
   ```

   Note that we specify the namespace. If not specified, it uses the current namespace context. You should see output similar to the following:

   ```console
   $ helm list -n helm-demo
   NAME           NAMESPACE REVISION  UPDATED                                 STATUS    CHART            APP VERSION
   guestbook-demo helm-demo 1         2020-02-24 18:08:02.017401264 +0000 UTC deployed  guestbook-0.2.0
   ```

   The `list` command provides the list of deployed charts (releases) giving information of chart version, namespace, number of updates (revisions) etc.

1. We now know the release is there from step 1., so we can update the application:

    ```console
    $ cd helm101/charts

    $ helm upgrade guestbook-demo ./guestbook --set redis.slaveEnabled=false,service.type=NodePort --namespace helm-demo
    Release "guestbook-demo" has been upgraded. Happy Helming!
    ...
    ```

    A Helm upgrade takes an existing release and upgrades it according to the information you provide. You should see output similar to the following:

    ```console
    $ helm upgrade guestbook-demo ./guestbook --set redis.slaveEnabled=false,service.type=NodePort --namespace helm-demo
    Release "guestbook-demo" has been upgraded. Happy Helming!
    NAME: guestbook-demo
    LAST DEPLOYED: Tue Feb 25 14:23:27 2020
    NAMESPACE: helm-demo
    STATUS: deployed
    REVISION: 2
    TEST SUITE: None
    NOTES:
    1. Get the application URL by running these commands:
      export NODE_PORT=$(kubectl get --namespace helm-demo -o jsonpath="{.spec.ports[0].nodePort}" services guestbook-demo)
      export NODE_IP=$(kubectl get nodes --namespace helm-demo -o jsonpath="{.items[0].status.addresses[0].address}")
      echo http://$NODE_IP:$NODE_PORT
    ```

    The `upgrade` command upgrades the app to a specified version of a chart, removes the `redis-slave` resources, and updates the app `service.type` to `NodePort`.

    Check the updates, using `kubectl get all --namespace helm-demo`:

    ```console
    $ kubectl get all --namespace helm-demo
    NAME                                  READY   STATUS    RESTARTS   AGE
    pod/guestbook-demo-6c9cf8b9-dhqk9     1/1     Running   0          20h
    pod/guestbook-demo-6c9cf8b9-zddn2     1/1     Running   0          20h
    pod/redis-master-5d8b66464f-g7sh6     1/1     Running   0          20h

    NAME                     TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)          AGE
    service/guestbook-demo   NodePort    172.21.43.244    <none>        3000:31202/TCP   20h
    service/redis-master     ClusterIP   172.21.12.43     <none>        6379/TCP         20h

    NAME                             READY   UP-TO-DATE   AVAILABLE   AGE
    deployment.apps/guestbook-demo   2/2     2            2           20h
    deployment.apps/redis-master     1/1     1            1           20h

    NAME                                        DESIRED   CURRENT   READY   AGE
    replicaset.apps/guestbook-demo-6c9cf8b9     2         2         2       20h
    replicaset.apps/redis-master-5d8b66464f     1         1         1       20h

    ```

    > Note: The service type has changed (to `NodePort`) and a new port has been allocated (`31202` in this output case) to the guestbook service. All `redis-slave` resources have been removed.

    When you check the Helm release with `helm list -n helm-demo`, you will see the revision and date has been updated:

    ```console
    $ helm list -n helm-demo
    NAME            NAMESPACE REVISION  UPDATED                                 STATUS    CHART            APP VERSION
    guestbook-demo  helm-demo 2         2020-02-25 14:23:27.06732381 +0000 UTC  deployed  guestbook-0.2.0
    ```

1. View the guestbook

   Get the public IP of one of your nodes:

   ```console
   kubectl get nodes -o wide
   ```

   Navigate to the IP address plus the node port that printed earlier.

## Conclusion

Congratulations, you have now updated the applications! Helm does not require any manual changing of resources and is therefore so much easier to upgrade! All configurations can be set on the fly on the command line or by using override files. This is made possible from when the logic was added to the template files, which enables or disables the capability, depending on the flag set.

Check out [Lab 3](../Lab3/README.md) to get an insight into revision management.
