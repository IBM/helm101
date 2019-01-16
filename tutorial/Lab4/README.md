# Lab 4. I like sharing

A key aspect of providing an application means sharing with others. Sharing can be direct counsumption (by users or in CI/CD pipelines) or as a dependency for other charts. If people can't find your app then they can't use it.

A means of sharing is a chart repository, which is a location where packaged charts can be stored and shared. As the chart repository only applies to Helm, we will just look at the usage and storage of Helm charts.

# Using charts from a public repository

Helm charts can be available on a remote repository or in a local environment/repository. The remote repositories can be public like [Helm Charts](https://github.com/helm/charts) or [IBM Helm Charts](https://github.com/IBM/charts), or hosted repositories like on Google Cloud Storage or GitHub. Refer to [Helm Chart Repository Guide](https://github.com/helm/helm/blob/master/docs/chart_repository.md) for more details. 

In this part of the lab, we show you how to install the `guestbook` chart from the [Helm101 repo](https://ibm.github.io/helm101/).

1. Check the repositories configured on your system:

   ```$ helm repo list```
   
   The output should be similar to the following:
   
   ```console
   NAME  	URL                                             
   stable	https://kubernetes-charts.storage.googleapis.com
   local 	http://127.0.0.1:8879/charts                   
   ```
   
   Note: The Helm charts repository is installed by default with Helm. It is installed with the repositories `local` and `stable`. You can run the `local` repo using the [helm serve](https://github.com/helm/helm/blob/master/docs/helm/helm_serve.md) command. The `stable` repo is located at `https://kubernetes-charts.storage.googleapis.com/`.

2. Add `helm101` repo:

   ```$ helm repo add helm101 https://ibm.github.io/helm101/```
   
   Should generate an output as follows:
   
   ```"helmm101" has been added to your repositories```
   
   You can also search your repositories for charts by running the following command, ```$ helm search helm101```:
   
   ```console
   NAME             	CHART VERSION	APP VERSION	DESCRIPTION                                                 
   helm101/guestbook	0.1.0        	           	A Helm chart to deploy Guestbook three tier web application.
   ```
      
3. Install the chart

   As mentioned we are going to install the `guestbook` chart from the [Helm101 repo](https://ibm.github.io/helm101/). As the repo is installed in our local respoitory we can reference the chart using the `repo name/chart name`, in other words `helm101/guestbook`. This means we can install the chart like we did previously with the command:

   ```$ helm install helm101/guestbook --name guestbook-demo --namespace repo-demo```
   
   The output should be similar to the following:
   
   ```console
   NAME:   guestbook-demo
   LAST DEPLOYED: Thu Dec 13 07:36:18 2018
   NAMESPACE: repo-demo
   STATUS: DEPLOYED
   
   RESOURCES:
   ==> v1/Service
   NAME                      TYPE          CLUSTER-IP     EXTERNAL-IP  PORT(S)         AGE
   guestbook-demo-guestbook  LoadBalancer  10.98.43.107   <pending>    3000:31241/TCP  2s
   redis-master              ClusterIP     10.103.16.208  <none>       6379/TCP        2s
   redis-slave               ClusterIP     10.99.249.122  <none>       6379/TCP        2s
   
   ==> v1/Deployment
   NAME                      READY  UP-TO-DATE  AVAILABLE  AGE
   guestbook-demo-guestbook  0/2    2           0          2s
   redis-master              0/1    1           0          2s
   redis-slave               0/2    2           0          2s
   
   ==> v1/Pod(related)
   NAME                                       READY  STATUS             RESTARTS  AGE
   guestbook-demo-guestbook-75f5f9cf84-jcbtb  0/1    Pending            0         2s
   guestbook-demo-guestbook-75f5f9cf84-lzsw4  0/1    ContainerCreating  0         2s
   redis-master-7b5cc58fc8-2bzw6              0/1    ContainerCreating  0         2s
   redis-slave-5db5dcfdfd-24249               0/1    ContainerCreating  0         2s
   redis-slave-5db5dcfdfd-nkcpt               0/1    ContainerCreating  0         1s
   
   
   NOTES:
   Get the application URL by running these commands:
   export NODE_PORT=$(kubectl get --namespace repo-demo -o jsonpath="{.spec.ports[0].nodePort}" services guestbook-demo-guestbook)
   export NODE_IP=$(kubectl get nodes -o jsonpath={.items[*].status.addresses[?\(@.type==\"ExternalIP\"\)].address})
   echo http://$NODE_IP:$NODE_PORT```
   
# Conclusion

This lab provided you with a brief introduction to the Helm repositories to show how charts can be installed. The ability to share your chart means ease of use to both you and your consumers.
