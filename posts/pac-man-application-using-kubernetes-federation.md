<!--
.. title: Pac-Man Application Using Kubernetes Federation
.. slug: pac-man-application-using-kubernetes-federation
.. date: 2017-03-10 17:33:34 UTC-08:00
.. tags: kubernetes,nginx,mongodb,pacman,game,gce,google compute engine
.. category: kubernetes
.. link:
.. description: Pac-Man Application deployed on federated Kubernetes cluster
.. type: text
-->

## Pac-Man Microservices Architecture Game Update

Previously I discussed the next phase of the
[Pac-Man + NGINX + PHP + MongoDB Kubernetes reference application](/posts/pacman-nginx-app/)
was to deploy the application using a
[Kubernetes Federation](https://kubernetes.io/docs/user-guide/federation/).
This was accomplished and you can
[read about the details here](https://github.com/font/k8s-example-apps/blob/master/pacman-nginx-app/docs/pacman-nginx-app-federated-cluster.md).
But in general, federation made it really easy to
manage multiple Kubernetes clusters through one pane of glass by deploying the Pac-Man Kubernetes
resources onto the clusters that are part of the federation. It automatically added the necessary
DNS entries for the services deployed to provide a load balanced application across all of the
clusters. This turned the Pac-Man microservices architecture game into a fully scalable
high-availability game.

## New Features

#### MongoDB Replica Set

Part of moving to a federation required a data replication strategy so your application's data could
scale horizontally as well. This required setting up a
[MongoDB Replica Set](https://docs.mongodb.com/manual/replication/). Unfortunately, MongoDB requires
many operations to only be performed on the primary. For example, MongoDB allows reading from many
instances, but only the primary can receive writes. In addition, adding replica set members can only
be done on the primary. Unfortunately this, combined with the fact that federation at the time of
this writing does not yet support things like
[StatefulSets](https://kubernetes.io/docs/concepts/abstractions/controllers/statefulsets/),
involved a bit of manual intervention to bootstrap the MongoDB replica set than I would like.

#### Pac-Man Zone

Once the application was deployed in the federation, I modified it to add a `Zone:` field that would
retrieve the zone of the Pac-Man game instance you were connecting to via the load balanced DNS.
That is, the federated DNS was updated to resolve to the set of IP addresses of the Pac-Man
game service in each of the federated zones to provide load balancing. Multiple refreshes of
the game would continually update the zone you were connecting to and display that for you
above your score. Once you were done playing and saved your high score, the zone
information was also saved along with it.

## Learnings

At first, I was creating the
[Kubernetes federation manually](https://github.com/font/kubernetes-cluster-federation/tree/v1.5.3)
but this became cumbersome and time consuming so I moved to use
[kubefed](https://kubernetes.io/docs/admin/federation/kubefed/) which really helped speed up
the process of creating a federated cluster.
[Steps for doing that are captured here](https://github.com/font/k8s-example-apps/blob/master/pacman-nginx-app/docs/kubernetes-cluster-federation.md).

In addition, I learned about some limitations with MongoDB and not having StatefulSets that may pose
problems as the application evolves.

## Next Steps

I'd like to see about performing coordinated migrations of the application as well as handling
failover. What would make those tests more interesting is having a larger volume of transactions
happening in the game, such as updating more data points on a regular interval defined in the game,
while performing those tests. In order to increase complexity in the game I've contemplated
migrating the backend to something more adept at handling that task. In any event, doing failover
tests may bump up against limitations of MongoDB and the lack of Statefulsets. Time will tell.


## GitHub Link

You can read all about the
[details of how to set up the Pac-Man microservices architecture game here](
https://github.com/font/k8s-example-apps/blob/master/pacman-nginx-app/docs/pacman-nginx-app-federated-cluster.md).
