---
title: "Six Things I learned From: Kubernetes Patterns: Reusable Elements for
  Designing Cloud-Native Applications"
date: 2020-04-26T22:03:45.655Z
description: "Six Things I learned From: Kubernetes Patterns: Reusable Elements
  for Designing Cloud-Native Applications"
---
### Introduction

Kubernetes Patterns: Reusable Elements for Designing Cloud-Native Applications is an excellent introduction to designing applications for Kubernetes. There is an overview of common patterns in Kubernetes. It provides a good overview for a developer interested in deploying applications to Kubernetes and provides a useful overview of the Kubernetes landscape. Overall I enjoyed the book and pulled quite a few useful tricks out of it, however, it's written at a high level and there will be points where I was wishing to went a little deeper.

 In particular, the chapters on sidecars the ambassador and adapter patterns, along with the chapters on probes, service discovery, and configuration were worth reading. There were clear and simple explanations of the various options that are available. Kubernetes is extremely complicated and presents many things which all do relatively similar stuff so having them all explained and the differences between being made clear were useful. 

### 6 things that I learned about Kubernetes

##### The multiple options that exist for probes

  The first thing that was really reinforced from reading this was about probes. There are a couple of different kinds of probes in Kubernetes. The main ones are liveness probes that issue some sort of TCP or HTTP request and are useful for services. They are also used to detect deadlocks. The other kind of probe is a readiness probe this is for things that are not HTTP services and instead, they issue some sort of command, for example, an echo. Both probes exist to make sure that a pod has started properly and one of them should be included in your deployments.



##### The difference between daemon sets and deployments and when you should use each.

 Daemon sets are similar in many ways to a daemon on a Unix system. So they are supposed to be operational or infrastructure-related processes. They also guarantee one pod per node or a subset of nodes. Deployments are for more standard services and applications. Most things that you deploy should use deployments but obviously if you are deploying something like Prometheus Daemonsets are a useful tool.

##### Controllers and Operators

Controllers exist to monitor a set of Kubernetes resources and keep them at the level that a declaration says they should be at. Deployments, statefulsets, and services are all examples of controllers. They do this by running a simple reconciliation process. You can also include additional data about what a controller does in the annotations, label or configmap. If you want to do something nonstandard with Kubernetes in a declarative way custom controllers are an option.

Operators build on top of controllers. They do this by also using a custom resource definition (CRD). An operator knows about Kubernetes and something else. For example, you could have a controller for Kafka that knows about Kubernetes and your Kafka setup and keeps everything in sync. If the CRD isn't flexible enough it's also possible to use the API extension hooks.

The difference between controllers and operators is fuzzy in many places. It's a spectrum between controllers and operators and it would be hard to draw a line in the sand between the two. Also there are resources for creating operators such as the operator framework. These usually are written in Go since that is the implementation language for k8s.

##### Sidecars and the ambassador and adapter pattern

When I first started using Kubernetes I thought of a pod as a container. This isn't true though and there can be multiple containers per pod.

The simplest version of this is the sidecar. Three major patterns use multiple containers that exist are sidecars, the ambassador pattern and the adapter pattern.

Both the ambassador pattern and the adapter patter build off of sidecars. A sidecar is just a way to include the second thing in a pod for example we could have something like log aggregation that ran as a sidecar in our pod. This allows each container to do only do one thing while having them still be kept together in a logical unit.

The adapter pattern is a specific kind of sidecar that translates data between two services. For example, you might output data using your logging tool and then translate it to a format that is easy for Prometheus to use. Ambassadors, on the other hand, work as a proxy. So if you wanted to connect to a database that changed depending on the environment you were in you could always just have your main application connect through the ambassador sidecar and then the ambassador would have the logic to figure out where to go.

##### How to Create Singleton Services in Kubernetes

Singleton services are very similar to singletons in object-oriented programming. It's a way to make it so there is one and only one pod running at a time. This is the opposite of normal Kubernetes scaling which will guarantee at least n numbers of a thing be running. We mostly want to use this with stateful services.

The interesting thing about singleton services is that we can still guarantee high availability because Kubernetes allows us to have an active-passive topology. This means that if the active pod fails we can start using the passive one.

The actual way that we create this in Kubernetes is with a statefulset with a single replica. This works since statefulsets favor consistency over availability, unlike a replicaset.

##### external services

This is much more of a minor trick that is buried in the Kubernetes docs also. You can create a service in a Kubernetes cluster that points to an external thing such as a database. There are some limitations and issues with this around HTTP because the headers will be funky but it's awesome for shared services that run outside of Kubernetes.

### Wrapping Up

I felt like I gained quite a bit of value from reading this and there are patterns that I've reached for since reading the book. If you already have a good understanding of Kubernetes feel free to skip the first part since it just covers existing Kubernetes resources. However, if you want to understand the difference between a daemon set and a deployment or how to use a readiness probe it's worth reading that.

The rest of the book is more advanced and is worth reading to gain a more advanced understanding of building applications for Kubernetes.