---
title:  "Azure Policy for AKS"
date:   2020-11-18 00:00:00 +0000
categories: Azure AKS
tags:
    - AKS
    - Azure
classes: wide
toc: true
header: 
    overlay_image: https://www.ans.co.uk/app/uploads/2020/11/iStock-913603748-min.jpg
    overlay_filter: rgba(0, 0, 0, 0.4)
published: true

---

# Azure Policy has gone into preview for AKS (Azure Kubernetes Service)

As Kubernetes has become a go-to container orchestration platform for organisations that are looking to deploy and scale their software, it has had to evolve its existing and new security feature sets.


To improve the security of your Azure Kubernetes Service (AKS) cluster, you can now secure your pods with Azure Policy (in preview). This integration enables you to deny and audit pod requests to an AKS cluster that violates policy. Users can choose from a list of 16 built-in options and two initiatives (which map to standards set in Kubernetes pod security policy) and apply those policies to secure pods.


Azure policy for AKS utilises the Open Source Policy Agent based on the Rego Policy Language. However, Microsoft has managed to deliver this in a less daunting way.


Instead of having to learn Rego, you can simply configure policies the same way you do for any other Azure resource (via the portal).


Some of the initial policies in preview include;
- Don’t allow privileged containers
- Containers should run with a read-only root file system
- Ensure CPU & Memory are inside the limits of the cluster
- Ensure only allowed container images are used
- FlexVolume should only use allowed storage drivers


Azure Policy for AKS can also be used to prevent resource creation if it is against a policy, and even provide a useful error response.


These policies are a critical component of any DevOps deployment, as Azure Policy establishes guard-rails for best practice.  With guard-rails in place, users are free to work without having to raise a hand to get permission from governance stakeholders (sometimes referred to as gatekeepers).


Azure Policy Add-on for Kubernetes service allows you to apply at-scale enforcements and safeguards on your clusters in a centralised, consistent manner.


[Head here](https://www.openpolicyagent.org/docs/latest/policy-language/) to find out more about Rego Policy Language. And if you want to know more about the integration, we found [this article](https://docs.microsoft.com/en-gb/azure/governance/policy/concepts/policy-for-kubernetes) really helpful.


Originally posted [here](https://www.ans.co.uk/azure-policy-has-gone-into-preview-for-aks-azure-kubernetes-service/)