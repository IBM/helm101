# Lab 3. Keeping track of the deployed application

Let's say you deployed different release versions of your application (i.e., you upgraded the running application). How do you keep track of the versions and how can you do a rollback?

# Revision management using Kubernetes

In this part of the lab, we should illustrate revision management of `guestbook` by using Kubernetes directly, but we can't. This is because Kubernetes does not provide any support for revision management. The onus is on you to manage your systems and any updates or changes you make. However, we can use Helm to conduct revision management.

# Revision management using Helm

In this part of the lab, we illustrate revision management on the deployed application `guestbook-demo` by using Helm.

With Helm, every time an install, upgrade, or rollback happens, the revision number is incremented by 1. The first revision number is always 1. Helm persists release metadata in configmaps, stored in the Kubernetes cluster. Every time your release changes, it appends that to the existing data. This provides Helm with the capability to rollback to a previous release.

Let's see how this works in practice.

1. Check the number of deployments:

    ```console
    $ helm history guestbook-demo --namespace helm-demo
    ```
    
    You should see output similar to the following because we did an upgrade in [Lab 2](../Lab2/README.md) after the initial install in [Lab 1](../Lab1/README.md):
    
    ```console
    REVISION        UPDATED                         STATUS          CHART           APP VERSION     DESCRIPTION     
    1               Wed Feb 19 18:02:11 2020        superseded      guestbook-0.2.0                 Install complete
    2               Wed Feb 19 18:14:43 2020        deployed        guestbook-0.2.0                 Upgrade complete
    ```
        
2. Roll back to the previous revision:

    In this rollback, Helm checks the changes that occured when upgrading from the revision 1 to revision 2. This information enables it to makes the calls to the Kubernetes API server, to update the deployed application as per the initial deployment - in other words with Redis slaves and using a load balancer.

    Rollback with this command, ```$ helm rollback guestbook-demo 1 --namespace helm-demo```
    
    ```console
    Rollback was a success! Happy Helming!
    ```
    Check the history again, `$ helm history guestbook-demo --namespace helm-demo`
    
    You should see output similar to the following:
    
    ```console
    REVISION        UPDATED                         STATUS          CHART           APP VERSION     DESCRIPTION     
    1               Wed Feb 19 18:02:11 2020        superseded      guestbook-0.2.0                 Install complete
    2               Wed Feb 19 18:14:43 2020        superseded      guestbook-0.2.0                 Upgrade complete
    3               Wed Feb 19 18:25:40 2020        deployed        guestbook-0.2.0                 Rollback to 1  
    ```
    
    To check the rollback, you can run `$ kubectl get all --namespace helm-demo`:
    
    ```console
    NAME                                READY     STATUS    RESTARTS   AGE
    pod/guestbook-demo-6c9cf8b9-dhqk9   1/1       Running   0          3h
    pod/guestbook-demo-6c9cf8b9-zddn2   1/1       Running   0          3h
    pod/redis-master-5d8b66464f-g7sh6   1/1       Running   0          3h
    pod/redis-slave-586b4c847c-tkfj5    1/1       Running   0          2m
    pod/redis-slave-586b4c847c-xxrdn    1/1       Running   0          2m
    
    NAME                     TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
    service/guestbook-demo   LoadBalancer   172.21.43.244   <pending>     3000:31202/TCP   3h
    service/redis-master     ClusterIP      172.21.12.43    <none>        6379/TCP         3h
    service/redis-slave      ClusterIP      172.21.232.16   <none>        6379/TCP         2m
    
    NAME                             DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
    deployment.apps/guestbook-demo   2         2         2            2           3h
    deployment.apps/redis-master     1         1         1            1           3h
    deployment.apps/redis-slave      2         2         2            2           2m
    
    NAME                                      DESIRED   CURRENT   READY     AGE
    replicaset.apps/guestbook-demo-6c9cf8b9   2         2         2         3h
    replicaset.apps/redis-master-5d8b66464f   1         1         1         3h
    replicaset.apps/redis-slave-586b4c847c    2         2         2         2m
    ```
   
    You can see from the output that the app service is the service type of `LoadBalancer` again and the Redis master/slave deployment has returned. 
    This shows a complete rollback from the upgrade in [Lab 2](../Lab2/README.md) 

# Conclusion

From this lab, we can say that Helm does revision management well and Kubernetes does not have the capability built in! You might be wondering why we need `helm rollback` when you could just re-run the `helm upgrade` from a previous version. And that's a good question. Technically, you should end up with the same resources (with same parameters) deployed. However, the advantage of using `helm rollback` is that helm manages (ie. remembers) all of the variations/parameters of the previous `helm install\upgrade` for you. Doing the rollback via a `helm upgrade` requires you (and your entire team) to manually track how the command was previously executed. That's not only tedious but very error prone. It is much easier, safer and reliable to let Helm manage all of that for you and all you need to do it tell it which previous version to go back to, and it does the rest.

[Lab 4](../Lab4/README.md) awaits.
