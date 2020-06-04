---
title: Amazon Elastic Kubernetes Service (EKS)
description: Enable Calico network policy in EKS.
---

### Big picture

Enable Calico in EKS managed Kubernetes service.

### Value

EKS has built-in support for Calico, providing a robust implementation of the full Kubernetes Network Policy API. EKS users wanting to go beyond Kubernetes network policy capabilities can make full use of the Calico Network Policy API.

You can also use Calico for networking on EKS in place of the default AWS VPC networking without the need to use IP addresses from the underlying VPC. This allows you to take
advantage of the full set of Calico networking features, including Calico's flexible IP address managemenet capabilities.

### How to

#### Install EKS with Amazon VPC networking

The geeky details of what you get:
{% include geek-details.html details='Policy:Calico,IPAM:AWS,CNI:AWS,Overlay:No,Routing:VPC Native,Datastore:Kubernetes' %}

To enable Calico network policy enforcement on an EKS cluster using the AWS VPC CNI plugin, follow these step-by-step instructions: {% include open-new-window.html text='Installing Calico on Amazon EKS' url='https://docs.aws.amazon.com/eks/latest/userguide/calico.html' %}

#### Install EKS with Calico networking

The geeky details of what you get:
{% include geek-details.html details='Policy:Calico,IPAM:Calico,CNI:Calico,Overlay:VXLAN,Routing:Calico,Datastore:Kubernetes' %}

For these instructions, we will use `eksctl` to provision the cluster. However, you can use any of the methods in {% include open-new-window.html text='Getting Started with Amazon EKS' url='https://docs.aws.amazon.com/eks/latest/userguide/getting-started.html' %}

Before you get started, make sure you have downloaded and configured the {% include open-new-window.html text='necessary prerequisites' url='https://docs.aws.amazon.com/eks/latest/userguide/getting-started-eksctl.html#eksctl-prereqs' %}

1. First, create an Amazon EKS cluster without any nodes.

   ```bash
   eksctl create cluster --name my-calico-cluster --without-nodegroup
   ```

1. Since this cluster will use Calico for networking, you must delete the `aws-node` daemon set to disable AWS VPC networking for pods.

   ```bash
   kubectl delete daemonset -n kube-system aws-node
   ```

1. Now that you have a cluster configured, you can install Calico.

   ```bash
   kubectl apply -f {{ "/manifests/calico-vxlan.yaml" | absolute_url }}
   ```

1. Finally, add nodes to the cluster.

   ```bash
   eksctl create nodegroup --cluster my-calico-cluster --node-type t3.medium --node-ami auto
   ```

   > **Tip**: See `eksctl create nodegroup --help` for the full set of node group options.
   {: .alert .alert-success}

### Above and beyond

- [Video: Everything you need to know about Kubernetes pod networking on AWS](https://www.projectcalico.org/everything-you-need-to-know-about-kubernetes-pod-networking-on-aws/)
- [Install calicoctl command line tool]({{ site.baseurl }}/getting-started/clis/calicoctl/install)
- [Get started with Kubernetes network policy]({{ site.baseurl }}/security/kubernetes-network-policy)
- [Get started with Calico network policy]({{ site.baseurl }}/security/calico-network-policy)
- [Enable default deny for Kubernetes pods]({{ site.baseurl }}/security/kubernetes-default-deny)