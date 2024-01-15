---           
layout: post
title: Optimizing Your Google Kubernetes Engine Costs - Part 1 - Basic Tweaks
date: 2023-12-13 00:00:00 IST
comments: true
categories: techops kubernetes cloud-cost-optimization
---

Introduction
============

In this series, I'll explore ways to optimize the cost of Google Kubernetes Engine (GKE) deployments. This series is for you if you are:

-   A developer who deploys on GKE

-   An Ops engineer who manages GKE clusters

-   Responsible for cloud architecture at your company

GKE is a managed Kubernetes platform on Google Cloud Platform (GCP). It gives you a Kubernetes control plane and uses GCP's compute, storage, and network resources.

Many teams move to GKE, get hit by rising cloud costs, and wonder if it was a good idea. Cost control on GKE is a mix of things. Getting the architecture right is as important as understanding the platform's tuning knobs. Equally important is to use the right monitoring and cost analysis tools and involve everyone in your team.

> In general, you have to treat cloud cost control as an engineering activity for it to be effective.

Some factors that can lead to uncontrolled and hidden costs on GKE:

-   Over-Provisioning

-   Unused services

-   Non-optimal application architecture

![Save](/assets/images/gke-cost-1.png)

In this post, I'll look at some basic things to optimize while setting up a new cluster and deploying your applications.

Creating the GKE Cluster
------------------------

### **Public IP Addresses for GKE VMs**

Each VM has an ephemeral public IP address if your cluster is not private. Each IP [costs a small amount](https://cloud.google.com/vpc/network-pricing) per hour. Even though it's small, over time it can add up if you have hundreds of nodes. Consider creating a [private cluster instead](https://cloud.google.com/kubernetes-engine/docs/how-to/private-clusters).

### **Logging and Monitoring**

Cloud Logging lets you access container logs in GKE's Stackdriver Log Viewer. You can enable it for system and workload (container) logs. Enabling Cloud Monitoring enables a [managed Prometheus instance](https://cloud.google.com/stackdriver/docs/managed-prometheus#gmp-billing-quotas).

> The cost of your observability stack is a function of your logs and metrics volume and retention. These costs can grow very high if your apps produce a lot of logs or metrics.

Before enabling, you should estimate the amount of logs your applications will generate. Add [Exclusion Filters](https://cloud.google.com/logging/docs/routing/overview#exclusions) to filter out logs that you don't want to store.

The GCP pricing calculator can help you estimate the cost of a managed Prometheus. See the Cloud Operations tab in the [calculator](https://cloud.google.com/products/calculator) to try it. If you find it difficult to calculate the cost of your logs or metrics upfront, do a short-term experiment. Enable these services and keep checking their cost in the GCP Billing portal every few days. If it is beyond your budget, switch to self-managed logging and monitoring stacks. These will come at the cost of having to self-manage these stacks.

Container logs and metrics are a must in any environment. You should not disable either of these features without having alternatives.

### **Regional Clusters**

Regional GKE clusters spread out your cluster's control plane and nodes across zones in the same region. The advantages of regional clusters are:

-   Increased availability during single-zone failures.

-   Better uptime during control plane upgrades.

Autopilot clusters - the default option in the GUI - are regional by default.

Regional clusters can cost more than zonal ones because:

-   Each node will have a copy (i.e. one node) in the zones covered by the regional cluster.

-   Node-to-node traffic between zones will add to the cost.

You should choose the level of availability that you want, and make the correct tradeoffs between cost and service. You can opt for a regional control plane, and keep your nodes in one zone, with the ability to failover to another.

### **Node Boot Disk Size**

You can choose the VM's boot disk size when you create a Node Pool. As of this writing, the default is 100 GB. If you want to change this later you have to recreate your Node Pool. 

How do you choose the right boot disk size? It depends. 

The node's OS uses the boot disk for its artifacts. The disk is also used to store the logs from the containers running on that node. In my experience, 20 GB is more than enough for the OS artifacts if you use [COS](https://cloud.google.com/container-optimized-os/docs/concepts/features-and-benefits). You can choose a disk size and keep monitoring its usage, and recreate the node pool if you see it's being underutilized.

Deploying Your Apps
-------------------

Watch out for these after your pods are running.

### **Persistent Volume Claims - Orphans and Over-provisioned Disks**

Pods with Persistent Volume Claims (PVCs) use a Storage Class to tell the cloud provider the type of disk to use. Storage Classes that use SSDs can incur [high costs](https://cloud.google.com/compute/disks-image-pricing#disk). You will pay for unused disk space if you over-provision a PVC with more space than your pod needs. Once it's created, you cannot downsize a PVC without downtime.

StatefulSets creates PVCs to store persistent data. If it's an operator-controlled StatefulSet, the operator should delete the PVCs when the StatefulSet pods scale down. Deleting a PVC will delete its underlying GCP disk. If the operator does not delete PVCs (i.e. it's a bug or it's not implemented), you can end up with orphaned disks. The disk costs will add to your cloud bill. Consider this:

-   An operator creates a StatefulSet with PVCs of 50 GB for each pod.

-   The StatefulSet scales up to 4 pods, using a total of 200 GB in PVCs.

-   The StatefulSet scales down to 2 pods. The operator does not delete the PVCs of the deleted pods, and the backing GCP disks live on.

-   You pay for 100GB that you are not using.

This has no straightforward solution, but you can try these:

-   Check that your operators are well-behaved and that they delete PVCs on pod scale-down.

-   If not, scan your PVC list for such orphans, and automate the process of deleting unused disks.

### **Container Vulnerability Scanning**

You can turn on Container Vulnerability Scanning in Google's Artifact Registry to scan new container images. As of this writing, scanning costs [$0.26](https://cloud.google.com/artifact-analysis/pricing#vulnz) per image. If that seems like a small number, consider this:

-   You have 20 microservices each with its image.

-   You build each microservice twice a day on average.

-   Total images/month = 20 x 2 x 30 = 1200. Scan cost/month = $312

Scanning is useful to keep your environment secure, but it's worth paying for only if you act on the scan results. It's easy for teams to enable this and forget.

* * * * *

In future posts, I'll talk about the bin packing problem, optimizing network data costs, using GCP's cost control features, and which cost monitoring tools to use.

Thank you for reading, and do reach out via comments or on [Twitter](https://twitter.com/talonx) if you want to chat or share your thoughts.

Photo by [micheile henderson](https://unsplash.com/@micheile?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash) on [Unsplash](https://unsplash.com/photos/green-plant-in-clear-glass-cup-SoT4-mZhyhE?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash).