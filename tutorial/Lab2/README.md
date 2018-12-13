# Lab 2. I need to change but none of the hassle

In [Lab 1](../Lab1/README.md) we installed the app. You are probably now thinking, I am done. In most circumstances that would be correct but what about any updates or improvements to the chart. How do you update your running app to pick up these changes? 

In this lab we are going to look at how to update our running app when the chart has been changed. To be able to demonstrate this we are going to make changes to the original `guestbook` chart as follows: 
* Remove the Redis slaves and therefore just use the in-memory DB
* Change from `LoadBalancer` type to `NodePort` type

It is slightly contrived but the end goal here is to show how to update using Kubernetes directly and Helm. So, how easy is it to do this? 

# Update the application using `kubectl`

In this part of the lab we will update the previously deployed application `guestbook`, using Kubernetes directly.

1. This is an optional step in that it is not necessary to update your running app. The reason for doing this step is "house keeping" - you want to have the correct files for the current configuration that you have deployed. This avoids making mistakes if you have future updates or even rollbacks. In this updated configuration, we remove the Redis slaves. That is why you should move/archive or remove the Redis slave files to keep abreast of the update:

   ```
   redis-slave-service.yaml
   redis-slave-deployment.yaml
   ```

2. Delete the Redis slave service and pods:

   ```console
   $ kubectl delete svc redis-slave --namespace default
   service "redis-slave" deleted
   $ kubectl delete deployment redis-slave --namespace default
   deployment.extensions "redis-slave" deleted
   ```

3. Update the guestbook service from `LoadBalancer` to `NodePort` type:

   ```$ sed -i.bak 's/LoadBalancer/NodePort/g' guestbook-service.yaml```

   Note: Like in Step 1., you may want to archive before making the changes..
   
4. Delete the guestbook service:
    
    ```$ kubectl delete svc guestbook --namespace default```
    
5. Recreate the service with `NodePort` type:

    ```$ kubectl create -f guestbook-service.yaml```
    
6. To check the updates, you can run ```kubectl get all --namespace default```:
    
    ```console
    NAME                                READY     STATUS    RESTARTS   AGE
    pod/guestbook-v1-7fc76dc46-9r4s7    1/1       Running   0          1h
    pod/guestbook-v1-7fc76dc46-hspnk    1/1       Running   0          1h
    pod/guestbook-v1-7fc76dc46-sxzkt    1/1       Running   0          1h
    pod/redis-master-5d8b66464f-pvbl9   1/1       Running   0          1h
    
    NAME                   TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
    service/guestbook      NodePort    172.21.45.29    <none>        3000:31989/TCP   31s
    service/kubernetes     ClusterIP   172.21.0.1      <none>        443/TCP          9d
    service/redis-master   ClusterIP   172.21.232.61   <none>        6379/TCP         1h
    
    NAME                           DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
    deployment.apps/guestbook-v1   3         3         3            3           1h
    deployment.apps/redis-master   1         1         1            1           1h
    
    NAME                                      DESIRED   CURRENT   READY     AGE
    replicaset.apps/guestbook-v1-7fc76dc46    3         3         3         1h
    replicaset.apps/redis-master-5d8b66464f   1         1         1         1h
    ```
    Note: The service type has changed (to `NodePort`) and a new port has been allocated (`31989` in this output case) to the guestbook 
    service. All `redis-slave` resources have been removed.

5. View the guestbook as per [Lab1](../Lab1/README.md), using the updated port for the guestbook service
   
# Update the application using Helm

In this part of the lab we will update the previously deployed application `guestbook-demo`, using Helm.

Before we start, let's take a few minutes to see how does Helm simplify the process compared to using Kubernetes directly. Helms' use of a [template language](https://docs.helm.sh/chart_template_guide/) provides great flexibility and power to chart authors which removes the complexity to the chart user. In the guestbook example, we are using the following capabilities of templating:
* Values: which is an object that provides access to values passed into the chart. An example of this is in `guestbook-service` which contains the following line: ```type: {{ .Values.service.type }}```. The line provides the capability to set the service type during upgrade or install.
* Control structures: (called “actions” in template parlance) provides the template author with the ability to control the flow of a template’s generation. An example of this is in `redis-slave-service` which contains the following line: ```{{- if .Values.redis.slaveEnabled -}}```. The line provides the capability to enable/disable the REDIS master/slave during upgrade or install.

Theory over, let's give it a go!

1. Update the application

    ```helm upgrade guestbook-demo ./guestbook --set redis.slaveEnabled=false,service.type=NodePort --namespace helm-demo```
    
    Helm upgrade takes an existing release and upgrades it according to the information you provide. You should see output similar to the following:
    
    ```console
    Release "guestbook-demo" has been upgraded. Happy Helming!
    LAST DEPLOYED: Mon Sep 24 10:36:18 2018
    NAMESPACE: helm-demo
    STATUS: DEPLOYED
    
    RESOURCES:
    ==> v1/Service
    NAME            AGE
    guestbook-demo  1h
    redis-master    1h
    
    ==> v1/Deployment
    guestbook-demo  1h
    redis-master    1h
    
    ==> v1/Pod(related)
    
    NAME                           READY  STATUS   RESTARTS  AGE
    guestbook-demo-6c9cf8b9-dhqk9  1/1    Running  0         1h
    guestbook-demo-6c9cf8b9-zddn2  1/1    Running  0         1h
    redis-master-5d8b66464f-g7sh6  1/1    Running  0         1h
    
    
    NOTES:
    Get the application URL by running these commands:
    export NODE_PORT=$(kubectl get --namespace helm-demo -o jsonpath="{.spec.ports[0].nodePort}" services guestbook-demo)
    export NODE_IP=$(kubectl get nodes -o jsonpath={.items[*].status.addresses[?\(@.type==\"ExternalIP\"\)].address})
    echo http://$NODE_IP:$NODE_PORT
    ```
    
    The `upgrade` command upgrades the app to a specified version of a chart, removes the `redis-slave` resources and updates the app 
    `service.type` to `NodePort`.
        
    To check the updates, you can run ```kubectl get all --namespace helm-demo```
    
    ```console
    NAME                                READY     STATUS    RESTARTS   AGE
    pod/guestbook-demo-6c9cf8b9-dhqk9   1/1       Running   0          1h
    pod/guestbook-demo-6c9cf8b9-zddn2   1/1       Running   0          1h
    pod/redis-master-5d8b66464f-g7sh6   1/1       Running   0          1h
    
    NAME                     TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
    service/guestbook-demo   NodePort    172.21.43.244   <none>        3000:31202/TCP   1h
    service/redis-master     ClusterIP   172.21.12.43    <none>        6379/TCP         1h
    
    NAME                             DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
    deployment.apps/guestbook-demo   2         2         2            2           1h
    deployment.apps/redis-master     1         1         1            1           1h
    
    NAME                                      DESIRED   CURRENT   READY     AGE
    replicaset.apps/guestbook-demo-6c9cf8b9   2         2         2         1h
    replicaset.apps/redis-master-5d8b66464f   1         1         1         1h
    ```
    Note: The service type has changed (to `NodePort`) and a new port has been allocated (`31202` in this output case) to the guestbook 
    service. All `redis-slave` resources have been removed.
    
2. View the guestbook as per [Lab1](../Lab1/README.md), using the updated port for the guestbook service

# Conclusion

Congratulations, you have now updated the applications! This is a "no brainer"; Helm does not require any manual changing of resouces and is therefore so much easier to upgrade! All configuration can be set on the fly on the command line or using override files. This is made possible by the upfront work done when the logic is added to the template files which enables or disables capability depending on the flag set.

Why not check out [Lab 3](../Lab3/README.md) next, to get an insight into revision management.
