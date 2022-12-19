# Upgrading K3s

I was reading the [official documentation](https://docs.k3s.io/upgrades/manual),
but was confused by the wording:
"To upgrade K3s from an older version you can re-run the installation script using the same flags"

What they really mean is to run the same commands used during the initial install.

On the **Control Plane**, you would run:

```shell
curl -sfL https://get.k3s.io | sh -
```

on **Worker Nodes** you would run something similar to:

```shell
curl -sfL https://get.k3s.io | K3S_URL=https://myserverIP:6443 K3S_TOKEN=mynodetoken sh -
```

refer to [Creating a K3s Cluster](k3s/k3sCluster.md#installing-on-worker-nodes) in this repo for more details.
