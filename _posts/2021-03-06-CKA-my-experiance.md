---
title:  "My experience taking the CKA"
date:   2021-03-06 00:00:00 +0000
categories: cka k8s exam
tags:
    - cka
    - k8s
    - kubernetes
    - exam
classes: wide
toc: true
header: 
    overlay_image: "https://i.imgur.com/8URUApP.jpg"
    overlay_filter: rgba(0, 0, 0, 0.4)
published: true


---

# My experience taking the CKA exam

On Thursday just gone, I passed the CKA (Kubernetes Certified Administrator) exam. There are many articles on medium with different peoples opinions and tips on how t o pass, however it would appear some parts of the exam have recently changed.

> The advise in the blog is valid from March 2021


## General info about the exam

- Length: 2 hours
- Cost: ~$300
- Number of questions: 15-20
- Topics & Weightings:

| Domain | Weighting |
|-|-|
| Cluster Architecture, Installation & Configuration | 25% |
| Workloads & Scheduling | 15% |
| Services & Networking |	20% |
| Storage |	10% |
| Troubleshooting	| 30% |


## Find a good course

I started my CKA revision a while ago by purchasing a [course on udemy](https://www.udemy.com/course/certified-kubernetes-administrator-with-practice-tests/) recommended to me by a colleague who had taken and passed the CKA back in Feb 2020.

> The course gets regular updates in line with the Kubernetes release plan. 


Unlike the majority of online proctored exams, the CKA is a lab based exam. 
Where most courses will just include a number of videos and presentations, [Mumshad Mannambeth / KodeKloud](https://www.linkedin.com/in/mmumshad/) provided a practice lab for each topic covered.

For example after learning about network policies there would be a lab to verify your knowledge on network policies.

## Practice Tests

Whilst taking the practice tests, try to use only the official documentation. In the actual exam, you are only allowed to have one browser tab in addition to the exam page. 

These pages only:
- [https://kubernetes.io/docs](https://kubernetes.io/docs)
- [https://github.com/kubernetes](https://github.com/kubernetes)
- [https://kubernetes.io/blog](https://kubernetes.io/blog)


That means you are not allowed to access discuss.kubernetes.io. 
Ensure you pay attention to this and attempt the practice tests with the allowed pages as it is up to you to not click them in the actual exam.

If you find a particular topic difficult during a practice test, try rewatching the video


## Key topics

There were a number of topics I initially found hard to grasp during my revision:
- [etcd backup/restore](https://kubernetes.io/docs/tasks/administer-cluster/configure-upgrade-etcd/)
- Persistent Volumes & Claims and how to configure them
    - Practice creating persistent volumes using hostPath and mounting them to a pod
- kubeadm upgrade
- RBAC
- Network Policies 
    - Not supported on flannel
- Cluster DNS for services and pods
    - DNS structure e.g. service-name.namespace.svc.cluster.local
    - Pod DNS structure e.g. 172-16-99-99.namespace.pod.cluster.local
- How to debug a failed application or cluster
    - Check if services are running in linux (Ubuntu)
    - Restart services in linux (Ubuntu)
    - Using apt to install a specific version of software (Ubuntu)
    - Using grep


## Before your exam 

Before the exam read ensure you read the official Handbook, Important Tips and FAQ

If the course you purchase has mock exams, ensure you are passing them with at least 80%

Practice navigating the official documentation, making a mental note of key things you can search for. As an example when you use the site search for persistent volume you can use CTRL + f to find the definition for a persistent volume claim by searching for `Kind: PersistentVolumeClaim`

## During your exam

Many suggest setting up an alias for `kubectl`, I didn't use this during my exam however it may help you if you arn't a fast typer
```bash
alias k=kubectl
```

Remember when using etcdctl to `export ETCDCTL_API=3`


