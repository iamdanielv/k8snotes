# Installing Portainer

If you want a web based view of your Kubernetes cluster, you can install Portainer. It's a very powerful UI that gives you a lot of control over the cluster and lets you deploy things without having to mess around with command line tools.

To learn more about Portainer go to [Portainer.io](https://www.portainer.io/)

This was really easy to deploy, I followed the instructions from [Portainer's Documentation](https://docs.portainer.io/start/install/server/kubernetes/baremetal) Basically, you use Helm - checkout [Installing Helm on k3s](k3sInstallHelm.md) if you need some help with the Helm install.

## **Step 1:** Add the Helm Repo

On your control-plane machine, begin by adding the Portainer Repo to Helm. In my sample my control-plane is on **nuc1**.

```shell
helm repo add portainer https://portainer.github.io/k8s/
helm repo update
```

You should see something similar to:

```shell
daniel@nuc1:~$ helm repo add portainer https://portainer.github.io/k8s/
"portainer" has been added to your repositories

daniel@nuc1:~$ helm repo update
Hang tight while we grab the latest from your chart repositories...
...Successfully got an update from the "portainer" chart repository
Update Complete. ⎈Happy Helming!⎈
```

## **Step 2:** Choose a way of accessing Portainer

I chose the "Expose via Load Balancer" method, but there are also instructions for doing it with Ingress and NodePort. You can explore and choose the one that fits your use case best. I am using the **Community Edition**, but you may also want to check out the **Business Edition**.

```shell
helm install --create-namespace -n portainer portainer portainer/portainer \
    --set service.type=LoadBalancer \
    --set tls.force=true
```

Helm should respond with something similar to:

```shell
daniel@nuc1:~$ helm install --create-namespace -n portainer portainer portainer/portainer \
    --set service.type=LoadBalancer \
    --set tls.force=true
NAME: portainer
LAST DEPLOYED: Wed Aug 24 11:21:41 2022
NAMESPACE: portainer
STATUS: deployed
REVISION: 1
NOTES:
Get the application URL by running these commands:
     NOTE: It may take a few minutes for the LoadBalancer IP to be available.
           You can watch the status of by running 'kubectl get --namespace portainer svc -w portainer'
  export SERVICE_IP=$(kubectl get svc --namespace portainer portainer --template "{{ range (index .status.loadBalancer.ingress 0) }}{{.}}{{ end }}")
  echo http://$SERVICE_IP:9443

```

It may take kubernetes a little while to bring up the pods. You can check the status with the command:

```shell
kubectl get --namespace portainer svc -w portainer
```

The command should respond with something similar to:

```shell
daniel@nuc1:~$ kubectl get --namespace portainer svc -w portainer
NAME        TYPE           CLUSTER-IP      EXTERNAL-IP                   PORT(S)                         AGE
portainer   LoadBalancer   10.43.253.242   192.168.3.100,192.168.3.108   9443:32435/TCP,8000:31339/TCP   87s
```

The portainer team provides a nice way of getting the URL. I've modified the command they give to point to the https endpoint since that is what we are using here:

```shell
export SERVICE_IP=$(kubectl get svc --namespace portainer portainer --template "{{ range (index .status.loadBalancer.ingress 0) }}{{.}}{{ end }}")
echo https://$SERVICE_IP:9443
```

You should see something similar to this on your console:

```shell
daniel@nuc1:~  export SERVICE_IP=$(kubectl get svc --namespace portainer portainer --template "{{ range (index .status.loadBalancer.ingress 0) }}{{.}}{{ end }}")")
echo https://$SERVICE_IP:9443
https://192.168.3.100:9443
```

In my case **nuc1**'s IP is **192.168.3.100**, and Portainer is exposed on port **9443**.
