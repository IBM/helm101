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
   Error: no repositories to show
   ```
   
   Note: No Helm charts repository is installed by default with Helm v3.1.

2. Add `helm101` repo:

   ```$ helm repo add helm101 https://ibm.github.io/helm101/```
   
   Should generate an output as follows:
   
   ```"helmm101" has been added to your repositories```
   
   You can also search your repositories for charts by running the following command, ```$ helm search repo helm101```:
   
   ```console
   NAME             	CHART VERSION	APP VERSION	DESCRIPTION                                                 
   helm101/guestbook	0.1.0        	           	A Helm chart to deploy Guestbook three tier web application.
   ```
      
3. Install the chart

   As mentioned we are going to install the `guestbook` chart from the [Helm101 repo](https://ibm.github.io/helm101/). As the repo is installed in our local respoitory we can reference the chart using the `repo name/chart name`, in other words `helm101/guestbook`. This means we can install the chart like we did previously with the command:

   ```$ helm install guestbook-demo-repo helm101/guestbook --namespace repo-demo```
   
   The output should be similar to the following:
   
   ```console
   NAME: guestbook-demo-repo
   LAST DEPLOYED: Wed Feb 19 18:38:35 2020
   NAMESPACE: repo-demo
   STATUS: deployed
   REVISION: 1
   TEST SUITE: None
   NOTES:
   1. Get the application URL by running these commands:
     NOTE: It may take a few minutes for the LoadBalancer IP to be available.
           You can watch the status of by running 'kubectl get svc -w guestbook-demo-repo --namespace repo-demo'
     export SERVICE_IP=$(kubectl get svc --namespace repo-demo guestbook-demo-repo -o jsonpath='{.status.loadBalancer.ingress[0].ip}')
     echo http://$SERVICE_IP:3000
   ```
   
4. Verify the new release

   To check the new release, you can run

   ```
   $ kubectl get all --namespace repo-demo
   ```

   The command should returns

   ```console
   NAME                                       READY   STATUS             RESTARTS   AGE
   pod/guestbook-demo-repo-7c65cfd8d6-2z7qc   1/1     Running            0          4m41s
   pod/guestbook-demo-repo-7c65cfd8d6-9b96v   1/1     Running            0          4m41s
   pod/redis-master-65b496655c-v7fbw          1/1     Running            0          4m41s
   pod/redis-slave-775f8c567f-6rb8b           0/1     ImagePullBackOff   0          4m41s
   pod/redis-slave-775f8c567f-m9zcz           0/1     ImagePullBackOff   0          4m41s

   NAME                          TYPE           CLUSTER-IP       EXTERNAL-IP     PORT(S)          AGE
   service/guestbook-demo-repo   LoadBalancer   172.21.245.164   169.46.44.178   3000:32674/TCP   4m41s
   service/redis-master          ClusterIP      172.21.22.93     <none>          6379/TCP         4m41s
   service/redis-slave           ClusterIP      172.21.21.52     <none>          6379/TCP         4m41s

   NAME                                  READY   UP-TO-DATE   AVAILABLE   AGE
   deployment.apps/guestbook-demo-repo   2/2     2            2           4m41s
   deployment.apps/redis-master          1/1     1            1           4m41s
   deployment.apps/redis-slave           0/2     2            0           4m41s

   NAME                                             DESIRED   CURRENT   READY   AGE
   replicaset.apps/guestbook-demo-repo-7c65cfd8d6   2         2         2       4m41s
   replicaset.apps/redis-master-65b496655c          1         1         1       4m41s
   replicaset.apps/redis-slave-775f8c567f           2         2         0       4m41s
   ```

# Conclusion

This lab provided you with a brief introduction to the Helm repositories to show how charts can be installed. The ability to share your chart means ease of use to both you and your consumers.
