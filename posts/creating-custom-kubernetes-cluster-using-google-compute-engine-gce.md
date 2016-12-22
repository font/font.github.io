<!-- 
.. title: Creating custom Kubernetes cluster using Google Compute Engine (GCE)
.. slug: creating-custom-kubernetes-cluster-using-google-compute-engine-gce
.. date: 2016-12-15 17:11:31 UTC-08:00
.. tags: tech gce kubernetes
.. category: kubernetes
.. link: 
.. description: Download and create a custom kubernetes cluster using Google Compute Engine (GCE)
.. type: text
-->

## Overview
If you're looking to get started with [Kubernetes](http://kubernetes.io/), there are 3 ways as of this writing:

1. Prebuilt Binary Release
1. Building from source
    - Note that if you're cloning Kubernetes from source, you will need to build
    a release in order to generate the necessary binaries in an archive for
    deployment. Building can take some time - so take a break.
1. Download Kubernetes and automatically set up a default cluster

We'll be discussing how to download any version of [Kubernetes](http://kubernetes.io/)
and automatically set up a default cluster as that's the quickest, most configurable
option. Particularly, we'll be focusing on downloading Kubernetes and creating
a customized deployment on [Google Compute Engine (GCE)](https://cloud.google.com/compute/).
However, there are also numerous others e.g. gke, aws, azure, vagrant, etc.

So, if you're looking to create a Kubernetes cluster on Google Compute Engine (GCE),
then an easy way to get started is by following the
[getting-started-guide](http://kubernetes.io/docs/getting-started-guides/gce/) for
GCE which we'll walk through briefly here.

## Prerequisites
Make sure to pay attention to the prereqs discussed
[here](http://kubernetes.io/docs/getting-started-guides/gce/#prerequisites) as they discuss making
sure that you have your Google Cloud Platform setup and configured properly for an
automatic deployment. Particularly, you'll want to make sure you can access the [Compute
Engine Instance Group Manager API](https://developers.google.com/console/help/new/#activatingapis),
make sure that `gcloud` is set to use the Google Cloud Platform project you want, and that
`gcloud` has the proper credentials. A good test for this is to run some `gcloud` commands to test
that you can create an instance and SSH into it:

```
$ gcloud compute instances create example-instance --image-family ubuntu-1604-lts --image-project ubuntu-os-cloud
Created [https://www.googleapis.com/compute/v1/projects/gce-kube-cluster/zones/us-west1-a/instances/example-instance].
NAME              ZONE        MACHINE_TYPE   PREEMPTIBLE  INTERNAL_IP  EXTERNAL_IP     STATUS
example-instance  us-west1-a  n1-standard-1               10.138.0.2   104.196.227.36  RUNNING
gcloud compute ssh example-instance
Updating project ssh metadata...|Updated [https://www.googleapis.com/compute/v1/projects/gce-kube-cluster].
Updating project ssh metadata...done.
Warning: Permanently added 'compute.2723409803693978954' (ECDSA) to the list of known hosts.
Welcome to Ubuntu 16.04.1 LTS (GNU/Linux 4.4.0-53-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  Get cloud support with Ubuntu Advantage Cloud Guest:
    http://www.ubuntu.com/business/services/cloud

0 packages can be updated.
0 updates are security updates.


To run a command as administrator (user "root"), use "sudo <command>".
See "man sudo_root" for details.

test@example-instance:~$
```

## Starting a cluster with default configuration
Now we're ready for an automatic download and deployment of the Kubernetes cluster in
our GCE project. Kubernetes allows automatic download and deployment of the cluster onto
one of many providers using the `KUBERNETES_PROVIDER` variable:

```
$ export KUBERNETES_PROVIDER=gce
```

If this variable is not specified, it uses `gce`, Google's Compute Engine, by default.

If you're ready to just accept the default options for instantiating the cluster using
the latest version of Kubernetes you can just run:
```
$ curl -sS https://get.k8s.io | bash
```
or

```
$ wget -q -O - https://get.k8s.io | bash
```

## Starting a cluster with custom configuration
However, sometimes you'd like to download a different version of Kubernetes and/or modify
some of the available GCE configuration options depending on your needs.
In order to do this, we want to allow the automated download of a specific version
of the Kubernetes binaries, but allow us to modify
parameters before the automated deployment. This is achieved by downloading the script
and saving it to a file:
```
$ curl -sS https://get.k8s.io -o kube.sh
```
or

```
$ wget -q https://get.k8s.io -O kube.sh
```

Then setting:

```
$ export KUBERNETES_RELEASE=v1.5.1
$ export KUBERNETES_SKIP_CREATE_CLUSTER=true
```

Feel free to set any version of Kubernetes that you'd like. Then run
```
$ bash kube.sh
```
After some interactive prompts, which can be avoided by setting `KUBERNETES_SKIP_CONFIRM=true`,
Kubernetes is downloaded and we can start to modify some parameters in the `cluster/gce/config-default.sh` file.

```
#!bash
$ cd kubernetes
$ grep -E '(\w)+=' cluster/gce/config-default.sh
KUBE_ROOT=$(dirname "${BASH_SOURCE}")/../..
GCLOUD=gcloud
ZONE=${KUBE_GCE_ZONE:-us-central1-b}
REGION=${ZONE%-*}
RELEASE_REGION_FALLBACK=${RELEASE_REGION_FALLBACK:-false}
REGIONAL_KUBE_ADDONS=${REGIONAL_KUBE_ADDONS:-true}
NODE_SIZE=${NODE_SIZE:-n1-standard-2}
NUM_NODES=${NUM_NODES:-3}
MASTER_SIZE=${MASTER_SIZE:-n1-standard-$(get-master-size)}
MASTER_DISK_TYPE=pd-ssd
MASTER_DISK_SIZE=${MASTER_DISK_SIZE:-20GB}
NODE_DISK_TYPE=${NODE_DISK_TYPE:-pd-standard}
NODE_DISK_SIZE=${NODE_DISK_SIZE:-100GB}
REGISTER_MASTER_KUBELET=${REGISTER_MASTER:-true}
PREEMPTIBLE_NODE=${PREEMPTIBLE_NODE:-false}
PREEMPTIBLE_MASTER=${PREEMPTIBLE_MASTER:-false}
KUBE_DELETE_NODES=${KUBE_DELETE_NODES:-true}
KUBE_DELETE_NETWORK=${KUBE_DELETE_NETWORK:-false}
MASTER_OS_DISTRIBUTION=${KUBE_MASTER_OS_DISTRIBUTION:-${KUBE_OS_DISTRIBUTION:-gci}}
NODE_OS_DISTRIBUTION=${KUBE_NODE_OS_DISTRIBUTION:-${KUBE_OS_DISTRIBUTION:-debian}}
CVM_VERSION=container-vm-v20161208
GCI_VERSION="gci-dev-56-8977-0-0"
MASTER_IMAGE=${KUBE_GCE_MASTER_IMAGE:-}
MASTER_IMAGE_PROJECT=${KUBE_GCE_MASTER_PROJECT:-google-containers}
NODE_IMAGE=${KUBE_GCE_NODE_IMAGE:-${CVM_VERSION}}
NODE_IMAGE_PROJECT=${KUBE_GCE_NODE_PROJECT:-google-containers}
CONTAINER_RUNTIME=${KUBE_CONTAINER_RUNTIME:-docker}
RKT_VERSION=${KUBE_RKT_VERSION:-1.14.0}
RKT_STAGE1_IMAGE=${KUBE_RKT_STAGE1_IMAGE:-coreos.com/rkt/stage1-coreos}
NETWORK=${KUBE_GCE_NETWORK:-default}
INSTANCE_PREFIX="${KUBE_GCE_INSTANCE_PREFIX:-kubernetes}"
CLUSTER_NAME="${CLUSTER_NAME:-${INSTANCE_PREFIX}}"
MASTER_NAME="${INSTANCE_PREFIX}-master"
INITIAL_ETCD_CLUSTER="${MASTER_NAME}"
ETCD_QUORUM_READ="${ENABLE_ETCD_QUORUM_READ:-false}"
MASTER_TAG="${INSTANCE_PREFIX}-master"
NODE_TAG="${INSTANCE_PREFIX}-minion"
MASTER_IP_RANGE="${MASTER_IP_RANGE:-10.246.0.0/24}"
CLUSTER_IP_RANGE="${CLUSTER_IP_RANGE:-10.244.0.0/14}"
    NODE_SCOPES="${NODE_SCOPES:-compute-rw,monitoring,logging-write,storage-ro,https://www.googleapis.com/auth/ndev.clouddns.readwrite}"
    NODE_SCOPES="${NODE_SCOPES:-compute-rw,monitoring,logging-write,storage-ro}"
EXTRA_DOCKER_OPTS="${EXTRA_DOCKER_OPTS:-}"
SERVICE_CLUSTER_IP_RANGE="${SERVICE_CLUSTER_IP_RANGE:-10.0.0.0/16}"  # formerly PORTAL_NET
ALLOCATE_NODE_CIDRS=true
ENABLE_DOCKER_REGISTRY_CACHE=true
ENABLE_L7_LOADBALANCING="${KUBE_ENABLE_L7_LOADBALANCING:-glbc}"
ENABLE_CLUSTER_MONITORING="${KUBE_ENABLE_CLUSTER_MONITORING:-influxdb}"
ENABLE_NODE_LOGGING="${KUBE_ENABLE_NODE_LOGGING:-true}"
LOGGING_DESTINATION="${KUBE_LOGGING_DESTINATION:-gcp}" # options: elasticsearch, gcp
ENABLE_CLUSTER_LOGGING="${KUBE_ENABLE_CLUSTER_LOGGING:-true}"
ELASTICSEARCH_LOGGING_REPLICAS=1
  EXTRA_DOCKER_OPTS="${EXTRA_DOCKER_OPTS} --insecure-registry 10.0.0.0/8"
RUNTIME_CONFIG="${KUBE_RUNTIME_CONFIG:-}"
FEATURE_GATES="${KUBE_FEATURE_GATES:-}"
ENABLE_CLUSTER_DNS="${KUBE_ENABLE_CLUSTER_DNS:-true}"
DNS_SERVER_IP="${KUBE_DNS_SERVER_IP:-10.0.0.10}"
DNS_DOMAIN="${KUBE_DNS_DOMAIN:-cluster.local}"
ENABLE_DNS_HORIZONTAL_AUTOSCALER="${KUBE_ENABLE_DNS_HORIZONTAL_AUTOSCALER:-true}"
ENABLE_CLUSTER_REGISTRY="${KUBE_ENABLE_CLUSTER_REGISTRY:-false}"
CLUSTER_REGISTRY_DISK="${CLUSTER_REGISTRY_PD:-${INSTANCE_PREFIX}-kube-system-kube-registry}"
CLUSTER_REGISTRY_DISK_SIZE="${CLUSTER_REGISTRY_DISK_SIZE:-200GB}"
CLUSTER_REGISTRY_DISK_TYPE_GCE="${CLUSTER_REGISTRY_DISK_TYPE_GCE:-pd-standard}"
ENABLE_CLUSTER_UI="${KUBE_ENABLE_CLUSTER_UI:-true}"
ENABLE_NODE_PROBLEM_DETECTOR="${KUBE_ENABLE_NODE_PROBLEM_DETECTOR:-true}"
ENABLE_CLUSTER_AUTOSCALER="${KUBE_ENABLE_CLUSTER_AUTOSCALER:-false}"
  AUTOSCALER_MIN_NODES="${KUBE_AUTOSCALER_MIN_NODES:-}"
  AUTOSCALER_MAX_NODES="${KUBE_AUTOSCALER_MAX_NODES:-}"
  AUTOSCALER_ENABLE_SCALE_DOWN="${KUBE_AUTOSCALER_ENABLE_SCALE_DOWN:-true}"
ENABLE_RESCHEDULER="${KUBE_ENABLE_RESCHEDULER:-true}"
ADMISSION_CONTROL=NamespaceLifecycle,LimitRanger,ServiceAccount,PersistentVolumeLabel,DefaultStorageClass,ResourceQuota
KUBE_UP_AUTOMATIC_CLEANUP=${KUBE_UP_AUTOMATIC_CLEANUP:-false}
STORAGE_BACKEND=${STORAGE_BACKEND:-}
NETWORK_PROVIDER="${NETWORK_PROVIDER:-kubenet}" # none, opencontrail, kubenet
OPENCONTRAIL_TAG="${OPENCONTRAIL_TAG:-R2.20}"
OPENCONTRAIL_KUBERNETES_TAG="${OPENCONTRAIL_KUBERNETES_TAG:-master}"
OPENCONTRAIL_PUBLIC_SUBNET="${OPENCONTRAIL_PUBLIC_SUBNET:-10.1.0.0/16}"
NETWORK_POLICY_PROVIDER="${NETWORK_POLICY_PROVIDER:-none}" # calico
HAIRPIN_MODE="${HAIRPIN_MODE:-promiscuous-bridge}" # promiscuous-bridge, hairpin-veth, none
E2E_STORAGE_TEST_ENVIRONMENT=${KUBE_E2E_STORAGE_TEST_ENVIRONMENT:-false}
EVICTION_HARD="${EVICTION_HARD:-memory.available<250Mi,nodefs.available<10%,nodefs.inodesFree<5%}"
SCHEDULING_ALGORITHM_PROVIDER="${SCHEDULING_ALGORITHM_PROVIDER:-}"
ENABLE_DEFAULT_STORAGE_CLASS="${ENABLE_DEFAULT_STORAGE_CLASS:-true}"
SOFTLOCKUP_PANIC="${SOFTLOCKUP_PANIC:-false}" # true, false
```

You can see there are many options. You can either edit the variables directly in the file or export the
environment variable that is used to set the default. Some of the more interesting options include:

- `ZONE`, `REGION` - for you zone and region snobs. Export the `KUBE_GCE_ZONE` environment
  variable to edit these settings.
- `NODE_SIZE` - the default of `n1-standard-2` is generally good for my use.
- `NUM_NODES` - the default of 3 nodes plus a master is probably okay for many tests.
  But for anything with higher demands such as a Ceph cluster, you may want to increase
  this.
- `MASTER_OS_DISTRIBUTION` and `NODE_OS_DISTRIBUTION` - This should match the name of the
  subdirectory within the `cluster/gce` directory. As of this writing only coreos, debian, gci,
  and trusty are available. In order to edit these settings you'll want to export
  `KUBE_MASTER_OS_DISTRIBUTION` and `KUBE_NODE_OS_DISTRIBUTION` to one of the available
  subdirectory options.
    - NOTE: it appears that the trusty distribution is not currently working due to
   [this issue](https://github.com/kubernetes/kubernetes/issues/39127).
- `MASTER_IMAGE`, `MASTER_IMAGE_PROJECT` and `NODE_IMAGE`, `NODE_IMAGE_PROJECT`- Default for
  the master is the `gci` image while nodes use `container-vm`, defined by the `GCI_VERSION`
  and `CVM_VERSION` variables respectively. To get a list of available images to use execute
  `gcloud compute images list`. To edit these settings export the `KUBE_GCE_MASTER_IMAGE`,
  `KUBE_GCE_MASTER_PROJECT`, `KUBE_GCE_NODE_IMAGE`, and `KUBE_GCE_NODE_PROJECT` to match the
  image name and project that corresponds to the image you want to use.

Edit to your hearts content, then follow it with:

```
$ ./cluster/kube-up.sh
```

If things go south or you want to correct some things, `ctrl-c` the execution and run:

```
$ ./cluster/kube-down.sh
```

Make your changes and re-launch.


## Conclusion
This is a pretty easy and quick way to get a custom Kubernetes cluster downloaded and
deployed onto Google Compute Engine (GCE) without much hassle. This is great if you find that you want
a particular version of Kubernetes with the ability to set some GCE configuration options.

---
