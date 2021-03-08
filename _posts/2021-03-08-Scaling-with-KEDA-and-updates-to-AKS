---
title:  "Scaling with KEDA and updates to AKS"
date:   2021-03-08 00:00:00 +0000
categories: keda k8s
tags:
    - keda
    - k8s
    - kubernetes
    - scaling
classes: wide
toc: true
header: 
    overlay_image: "https://www.ans.co.uk/app/uploads/2021/03/iStock-623381110.jpg"
    overlay_filter: rgba(0, 0, 0, 0.4)
published: true


---

# Scaling with KEDA and updates to AKS


In a recent [AKS Public Office Hours](https://aka.ms/akspublicofficehours) there were a number of awesome announcements:

- [Containerd](https://containerd.io/) will become the default container runtime in AKS >=1.19.x, this has been in preview for a while and many will want to jump on it for the security, speed and stability it provides however Microsoft recommends testing your workloads with containerd before upgrading your production clusters to v1.19 https://docs.microsoft.com/en-us/azure/aks/cluster-configuration#container-runtime-configuration-preview
- Microsoft have released their maxSurge capability which allows concurrent virtual machines (VM) in a cluster to be upgraded at any one time which as many AKS cluster admins will find a massive help as large clusters take a long time to upgrade at the moment due to it doing a VM at a time.
- Microsoft have announced their AKS image aligns to Microsoft Security Baseline which conforms to Linux and K8S (Kubernetes) benchmarks for CIS hardened images https://docs.microsoft.com/en-us/azure/aks/security-hardened-vm-host-image

 

###### **Your current HPA (Horizontal Pod Autoscaler) not cutting it? KEDA could be the solution.**

[KEDA](https://keda.sh/) is a Kubernetes-base event driven autoscaler. With KEDA, you can drive the scaling of any container in Kubernetes based on the number of events needing to be processed.

KEDA Core Features

- Event-Driven

Intelligently scale your event-driven application (Blob Storage, Storage Queues, Event Hub, Log Analytics)

- Simple autoscaling

Bring rich scaling to every container in your Kubernetes cluster

- Built-in Scalers

Out-of-the-box scalers for various vendors, databases, messaging services, telemetry services and more

-  Multiple Workload Types

Support for a variety of workload types such as deployments and jobs

-  Vendor Agnostic

Support for triggers across multiple vendors

-  Azure Functions Support

Run and scale your Azure Functions on Kubernetes in production workloads

Most Kubernetes users are aware of the horizontal pod auto-scaler which can scale deployments, replica-sets and jobs by using data form to the metrics server to monitor the resource demand.

 

###### **How to scale with KEDA**

CPU and Memory (which is the most common metrics used in HPAs) isn’t necessarily a great indication of event driven scale that’s coming into your application, so how can we actually find a way to make that extensible and start scaling our pods um in a more event-driven fashion?

With KEDA you can scale custom resources in addition to the standard resource and scale based on external trigger sources.

If your application is currently hosted on Azure/AKS, the community has got you covered with a wide range of integrations. Using service bus queues to process data. You can scale the service up or down based on the number of pending messages, enabling you to save compute during quiet periods and rapidly scale up during critical times.

A further list of scalers can be found [here](https://keda.sh/docs/2.1/scalers/)

The most common example for scaling is e-commerce, for example if your company was to announce a new product, you would want to ensure your site can handle the additional traffic to ensure customers new and old can use the site without it running out of capacity.

However, scaling applies to almost all production workloads, from healthcare to legal not just e-commerce. You may want to scale up your system during the day when requests are high and scale down over night or over the weekend due to inactivity, this could be done manually.

 

###### **What is the benefit of using KEDA over and HPA?**

Many organisations have data tasks that don’t require much CPU or memory. To complete them in a timely manner, we could scale horizontally using KEDA, triggering on a queue as the task may have never scaled with an HPA as it is more bound by lots of small requests rather than CPU/memory intensive requests.

This is particularly useful in industries such as insurance. Over the course of the day you may be receiving requests from users as people phone up to make a claim, however when system utilisation is lower you may run some batch jobs which calculate and process these claims. These are normally stored in some form of queue. These jobs are not typically CPU or memory intensive, instead they are business logic heavy. They can take a long time to complete, for larger organisations it could be numerous hours. If the system was able to scale based on the length of the queue, it could more than half the time it takes to complete.

You can find out more about the latest release of KEDA [here](https://keda.sh/docs/2.0/) and an example you can deploy yourself [here](https://github.com/kendallroden/sample-dotnet-worker-servicebus-queue)
