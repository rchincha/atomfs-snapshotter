# atomfs-snapshotter
containerd snapshotter for atoms

# Run container with the `atomfs` snapshotter

## Install snapshotter

Install containerd remote snapshotter plugin (atomfs-snapshotter-grpc)

## containerd Configuration

Add the following to /etc/containerd/config.toml:

```
[proxy_plugins.atomfs]
# tells containerd that the atomfs plugin is a snapshotter and implements the snapshotter API
type = "snapshot"
# tells containerd where to connect to the atomfs snapshotter
address = "/run/atomfs-snapshotter-grpc/atomfs-snapshotter-grpc.sock"
[proxy_plugins.atomfs.exports]
  # defines the root data directory for the atomfs snapshotter. Kubernetes uses
  # this to calculate disk utilization, enforce storage limits, and trigger
  # garbage collection.
  root = "/var/lib/atomfs-snapshotter-grpc"

[plugins."io.containerd.grpc.v1.cri".containerd]
  # tells containerd to use atomfs by default.
  snapshotter = "atomfs"
```

## Run/test snapshotter

```
bin/atomfs-snapshotter-grpc /run/atomfs-snapshotter-grpc/atomfs-snapshotter-grpc.sock /tmp/atomfs/

sudo containerd -c tests/containerd.toml

hack/tools/bin/nerdctl --snapshotter atomfs run public.ecr.aws/docker/library/busybox:stable
```
