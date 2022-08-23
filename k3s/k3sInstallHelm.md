# Installing helm on k3s

Now that we have a k3s cluster, we can install Helm as our package / software manager for kubernetes.

This mostly comes from the [official documentation](https://helm.sh/docs/intro/install/)  

## Pre-Req's

Even though it is possible to run a single node, to really test the cluster you will need at least two nodes. If you followed the [Creating a K3s cluster](k3sCluster.md) document you should already have something similar.

* Node 1 will be the master/main node - aka Control Plane (nuc1)
* Node 2 will be a worker node (nuc2)

## Install with installation script

There are several ways of installing Helm that you can check out at the install link. I have chosen to use the installation script.

>WARNING: Piping a random script from the internet into your shell and running it is very dangerous. I've included the piped command to make it easier to cut and paste, but you should download the script and look at it to make sure it isn't doing anything dangerous.

The Helm project has provided a nice [installation script](https://helm.sh/docs/intro/install/#from-script) that you can use to automate the install. I've simplified the steps here, but you should really read the contents of the script before piping it to your shell.

On your control-plane **(nuc1)**, run the following command:

```shell
curl -sfL https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 | bash -
```

You can skip the "Alternative Install" and jump to [Configure Helm to use k3s](#configure-helm-to-use-k3s)

## Alternative Install - Binary Download

If you don't feel comfortable trusting the script, you can manually download the release binary and place it in your path. For more details, check out [Installing the Binary Release](https://helm.sh/docs/intro/install/#from-the-binary-releases)

## Configure Helm to use k3s

k3s stores it's configuration file at `/etc/rancher/k3s/k3s.yaml`; however, helm by default looks in `~/.kube/config`.

### For **Development** use

We can copy the config file over to the user's directory with the following commands:
  
```shell
mkdir ~/.kube
sudo cp /etc/rancher/k3s/k3s.yaml ~/.kube/config
sudo chown $(id -nu):$(id -ng) ~/.kube/config
```

Now we need to edit the `~/.profile` file and add an environment variable. You can use your editor of choice, but here I have used nano.

```shell
nano ~/.profile
```

at the end append

```shell
export KUBECONFIG=~/.kube/config
```

You can now either start a new shell to get the updated environment variable or you can type:

```shell
source ~/.profile
```

You should now be able to use the `kubectl` and `helm` commands without requiring sudo. **Note:** This is not really secure and should only be used during development.

### For **Production** use

We would take advantage of the environment variable `export KUBECONFIG=/etc/rancher/k3s/k3s.yaml` and NOT copy the file to the local user's directory
