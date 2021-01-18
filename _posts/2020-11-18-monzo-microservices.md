---
title:  "Banking on kubernetes: Are microservices the future of flexible, digital banking?"
date:   2020-11-18 00:00:00 +0000
categories: Kubernetes
tags:
    - Kubernetes
    - Microservices
	- AWS
classes: wide
toc: true
header: 
    overlay_image: https://www.ans.co.uk/app/uploads/2020/11/iStock-1179477379.jpg
    overlay_filter: rgba(0, 0, 0, 0.4)
published: true

---

By 2025, it is estimated that [44% of brits](https://www.finder.com/uk/digital-banking-adoption) will have a digital-only bank. Since 2015, Monzo has been a driving force in this space, amassing over 4 million customers. At KubeCon 2020, Senior Monzo Engineers Miles Bryant and Suhail Patel shared their journey of self-hosting a production Kubernetes deployment.

Monzo had some time to make some long-term architectural decisions for their platform and wanted to get this right because nobody wants to replace the core technology that underpins a running bank.

Back in 2016 when they had just started building out their next-gen banking infrastructure, they needed a platform to run these microservices.

The first choice was [mesos and marathon, ](https://mesosphere.github.io/marathon/)however they quickly ran into limitations and a lacking feature set. So Monzo took a bold decision on a new open source project called Kubernetes.



###### **Why choose Kubernetes for Microservices?**

Monzo wanted their systems to be highly extensible, scalable, resilient and secure and naturally chose microservice architecture as a fit for most of these considerations.

![Number of Monzo Microservices over time](https://www.ans.co.uk/app/uploads/2020/11/Will-blog-1.png)

Monzo now has over 1600 microservices, Miles shared a simplified architecture diagram, every one of the 1600 dots is an microservice, each one has a line which represents a link to any connected microservices. The architecture allows them to be really flexible in how they scale their services, organise teams and add new engineers.

![Monzo Microservices Graph](https://www.ans.co.uk/app/uploads/2020/11/Will-blog2.png)

Kubernetes is moving closer and closer to a world where you can easily extend and customise without having to reconfigure the entire control plane components but that hasn’t been Monzo’s experience over the past couple of years, it’s only now that the Kubernetes project is moving in that direction where they’re splitting off into more and more components almost like they’re microservices.



###### **What are the benefits of using Kubernetes?**

As very early adopters, Monzo didn’t have the option for a managed cluster from the likes of Google Cloud, AWS or Azure, instead they had to self-manage on top of AWS. Suhail commented that if they were starting from scratch today, they’d probably want to go for a managed service.

Some of the benefits Monzo saw by managing their own cluster was they had picked up a lot of expertise and understanding of how Kubernetes worked under the hood and have now been able to advise other engineers in the industry who run into similar issues. Monzo even have the knowledge to dive deep into the Kubernetes stack to fix low level problems themselves.

Monzo were able to diagnose a CPU throttling issue, It was discovered when some of their latency sensitive services were seeing high latency (in the 99th percentile).

When they investigated more, they found these services were using a lot of CPU and were actually being paused by the kernel for 100 milliseconds. However, the hundred milliseconds figure was a tuneable parameter in the kernel, but Kubernetes used to hard code this. Monzo worked with the open source community to come up with a fix and as they managed their own cluster, they were able to test with the affected services before submitting it as a working fix.

 

###### **What other success have Monzo had with Kubernetes?**

One of their best examples of where self-hosting had really helped Monzo over the years, they got used to Kubernetes evolving quickly and one thing they had become experts in is doing complex upgrades and migrations. In this particular example they managed to do a rolling migration of the overlay network on a live cluster so when they first started using Kubernetes they decided to use the flannel overlay network which at the time was the most popular network and a couple years later they were still running it in the really slow vxlan mode.

If you haven’t come across this, vxlan mode essentially works by wrapping all your layer 2 traffic in udp packets but crucially the wrapping and unwrapping happens via flannel proxies which runs in user space which is incredibly slow and eventually they were running into the limits of this.

Monzo were also quite keen to start using network policies to lock down traffic between different pods and calico seemed like a natural fit to both solve the problem of our networking being slow and low bandwidth and to start using network policies.

The sane way of doing this is to spin up an entirely new cluster with the new kind of network overlay and you shift your traffic across to it, but this would have been pretty tricky for Monzo due to some unique constraints at the time.

Firstly, they had some stateful workloads in the cluster and at the time splitting these workloads across two clusters and balancing between them wasn’t an easy problem to solve.

Secondly, they also had some workloads which had persistent connections to payment services, and these would have also been quite tricky to split across the two clusters without downtime and as a bank, planned downtime is something you really want to avoid at all costs.

Monzo did manage to find a way to run both overlay networks in the same cluster at the same time, they deployed two different node groups one with flannel and one with calico. Both had separate IP ranges and every node on the cluster was configured to be able to route to any other node regardless of what overlay network it was using.

This was ideal for them because they could slowly ramp up the number of calico nodes they had and tune down a number of flannel nodes and if there was an issue, they could quickly roll back by going in the other direction.


Originally posted [here](https://www.ans.co.uk/are-microservices-the-future-of-flexible-digital-banking/)