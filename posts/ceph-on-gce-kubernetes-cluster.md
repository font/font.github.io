<!-- 
.. title: Deploying Containerized Ceph on a Kubernetes Cluster Using Google Compute Engine
.. slug: ceph-on-gce-kubernetes-cluster
.. date: 2017-02-03 11:38:03 UTC-08:00
.. tags: ceph, storage, kubernetes, cloud, gce, google compute engine
.. category: kubernetes
.. link: 
.. description: Discussing how to deploy containerized Ceph on
                a Kubernetes cluster running on Google Compute Engine.
.. type: text
-->

## Quick Summary

Recently we've been hard at work trying to set up dynamic provisioning of Ceph Rados
Block Device storage on a Kubernetes cluster. This required:

- Kubernetes version >= 1.5
- The ability to install Ceph and RBD packages on all host nodes i.e. master and slaves

Unfortunately we could not use Google Container Engine (GKE) for this because
at first, it did not support version 1.5 since it was too new.
Google quickly resolved that and has been really
good at keeping up-to-date with the latest Kubernetes releases. However, the
subsequent problem is that we needed to install Ceph and RBD packages on all host
nodes. Even though Google does provide a `CONTAINER_VM` image that is able to
install packages, GKE does not provide access to the master to install the necessary
packages. This seems like a limitation with GKE. So it was decided to move towards
a manual deployment of a Kubernetes cluster. For the manual deployment we used
Google Compute Engine and followed the nice example Kelsey Hightower provides:

- [Kubernetes the Hard Way](https://github.com/kelseyhightower/kubernetes-the-hard-way)

At first, I was hitting a DNS problem until I realized that there was a firewall rule missing for
the POD CIDR that was being used. See [issue 88](https://github.com/kelseyhightower/kubernetes-the-hard-way/issues/88) and the [pull request](https://github.com/kelseyhightower/kubernetes-the-hard-way/pull/117) that resolves it for more details. It was also nice to script the deployment
so we created some scripts for that as well but they have not yet been merged. In the
meantime you can get them here:

- [https://github.com/font/kubernetes-the-hard-way/tree/scripts](https://github.com/font/kubernetes-the-hard-way/tree/scripts)

In the end it was good to understand how the different components of Kubernetes
fit together and be able to debug it a bit.

After Kubernetes was deployed we deployed Ceph and went about testing CephFS and RBD
volume mounts, static provisioning of CephFS and RBD persistent volume claims, as
well as dynamic provisioning of RBD persistent volume claims and they all worked great!

## Documentation

If you're interested you can get the detailed documentation here:

- [https://github.com/ceph/ceph-docker/tree/master/examples/kubernetes/gce](https://github.com/ceph/ceph-docker/tree/master/examples/kubernetes/gce)

### Video Demonstration

In the documentation you'll also find a link to this video demonstration that walks through those
steps:

<iframe width="560" height="315" src="https://www.youtube.com/embed/ic38-19wIGY" frameborder="0" allowfullscreen></iframe>

### Enjoy!
