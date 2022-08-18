# Creating a K3s cluster

Creating a [k3s](https://k3s.io/) cluster is relatively simple, but there were some things that I thought could be spelled out a little more.

## Pre-Req's

Even though it is possible to run a single node, to really test the cluster you will need at least two nodes.

* Node 1 will be the master/main node - aka Control Plane
* Node 2 will be a worker node

## Installing k3s on Control Plane

>WARNING: Piping a random script from the internet into your shell and running it is very dangerous. I've included the piped command to make it easier to cut and paste, but you should download the script and look at it to make sure it isn't doing anything dangerous.

**Node 1** will be our control plane, in order to install k3s, you can run the following script:

```shell
curl -sfL https://get.k3s.io | sh -
```


The script will ask for your sudo password and download all the required tools. It should look something like this once it's finished:

```shell
[INFO]  Verifying binary download
[INFO]  Installing k3s to /usr/local/bin/k3s
[INFO]  Skipping installation of SELinux RPM
[INFO]  Creating /usr/local/bin/kubectl symlink to k3s
[INFO]  Creating /usr/local/bin/crictl symlink to k3s
[INFO]  Skipping /usr/local/bin/ctr symlink to k3s, command exists in PATH at /usr/bin/ctr
[INFO]  Creating killall script /usr/local/bin/k3s-killall.sh
[INFO]  Creating uninstall script /usr/local/bin/k3s-uninstall.sh
[INFO]  env: Creating environment file /etc/systemd/system/k3s.service.env
[INFO]  systemd: Creating service file /etc/systemd/system/k3s.service
[INFO]  systemd: Enabling k3s unit
Created symlink /etc/systemd/system/multi-user.target.wants/k3s.service → /etc/systemd/system/k3s.service.
[INFO]  systemd: Starting k3s
```

The Installer will create a link to the k3s command when you type `kubectl`

```shell
/usr/local/bin/kubectl -> k3s
```

in your terminal you can type the following to see the current state of kubernetes:

```shell
sudo kubectl get nodes
```

it should respond with something similar to:

```shell
daniel@nuc1:~$ sudo kubectl get nodes
NAME   STATUS   ROLES                  AGE     VERSION
nuc1   Ready    control-plane,master   4m25s   v1.24.3+k3s1
```

Here we see that my master/main/control-plane node is named `nuc1` and it is currently in `Ready` state.

## Alternative - Running without sudo -- LESS Secure

If you don't run as root or use sudo you will get an error similar to:

```shell
daniel@nuc1:~$ kubectl get nodes
WARN[0000] Unable to read /etc/rancher/k3s/k3s.yaml, please start server with --write-kubeconfig-mode to modify kube config permissions 
error: error loading config file "/etc/rancher/k3s/k3s.yaml": open /etc/rancher/k3s/k3s.yaml: permission denied
```

you can change the permissions of the `k3s.yaml` file so you don't have to use sudo every time, but that reduces the security of your installation. It is up to you if you are ok with taking the risk.

```shell
sudo chmod 644 /etc/rancher/k3s/k3s.yaml
```