+++
title = "Fleet Configuration Management"
date = "2025-04-07"
description = "This post explains the challenge of fleet configuration management, the role of an automated fleet configuration management system, and describes key considerations for building such a system."

[taxonomies]
tags = ["Microsoft ISE", "Software", "DevOps", "CI/CD", "Kubernetes"]

[extra]
toc = true
local_image = "img/fleet-configuration-management/configuration-management-system.png"
+++

# Introduction

Recently, my team at Microsoft completed a customer engagement that involved managing edge deployments onto factory floors across geographical locations. This system needed to support existing workloads migrated from Azure IoT Hub, new workloads for processing IoT data feeds, and machine learning workloads for performing inference to ship intelligence to the edge.

During this engagement, we built a system to handle this complexity using [Kalypso](https://github.com/microsoft/kalypso) to orchestrate GitOps deployments across a fleet of [arc-enabled Kubernetes clusters](https://learn.microsoft.com/en-us/azure/azure-arc/kubernetes/overview) hosting [Azure IoT Operations](https://learn.microsoft.com/en-us/azure/iot-operations/overview-iot-operations) to support cloud connectivity and IoT device telemetry.

This post explains the challenge of fleet configuration management, the role of an automated fleet configuration management system, and describes key considerations for building such a system.

## What Is Fleet Configuration Management?

An automated fleet configuration management system is essential in a scenario where many workloads or applications need to be deployed and managed across a fleet of hosting platforms. This can be the case for cloud hosted platforms serving separate purposes, or edge hosted platforms where workloads need to be distributed across separate locations.

## Example Fleet Configuration Management Systems

There are a few options for implementing a fleet configuration management system. The one that works best for a given scenario will depend on details such as the hosting platforms that they support, licensing, and operational requirements. All of these options require managing a single separate Kubernetes cluster to run the automation work of the system.

- [Azure Kubernetes Fleet Manager](https://learn.microsoft.com/en-us/azure/kubernetes-fleet/overview): This is a good choice for cloud-hosted AKS workloads and provides features for orchestrating Kubernetes cluster operations such as cluster version upgrades. A "Hub Cluster" hosts a Kubernetes controller that reconciles all of the workloads running on member clusters.
- [Rancher Fleet](https://fleet.rancher.io/): This is a good choice if you are using Rancher to manage your Kubernetes clusters. It is [open source](https://github.com/rancher/fleet) and uses a Fleet Agent to manage individual workloads on clusters within the fleet. A "Fleet Controller Cluster" hosts a Kubernetes controller and is monitored by all of the individual fleet clusters.
- [Kalypso](https://github.com/microsoft/kalypso): This is a good choice for full flexibility as it is compatible with edge or cloud-hosted clusters and works with any GitOps agents (Flux, ArgoCD, etc.). The Kalypso scheduler runs on a designated Kubernetes cluster and orchestrates pull requests to GitOps repositories that are monitored by separate GitOps clusters. See [Example Architecture With Kalypso](#example-architecture-with-kalypso) below for an example architecture using Kalypso.

## Definitions

Before getting into the details, lets first define terms as they appear in this post.

- A **fleet** is a collection of hosting platforms that are managed together.
- A **workload** is a piece of software that is deployed as a unit onto the **fleet**.
- **Configuration** represents a specific permutation of settings and specifications to make an instance of a **workload** run correctly in the **fleet**.

Generally, a **fleet** is composed of many hosting platforms, and there are many **workloads** that are deployed, each with a unique set of **configuration**.

The specific details will depend on what the hosting platform and workloads are, but for this post, we will use [Kubernetes](https://kubernetes.io/) clusters as the **fleet**, [Helm](https://helm.sh/) charts as **workloads**, and rendered [Kubernetes objects (manifests)](https://kubernetes.io/docs/concepts/overview/working-with-objects/) as **configuration**. We will also assume that the **workloads** are deployed using [GitOps](https://learn.microsoft.com/en-us/azure/architecture/example-scenario/gitops-aks/gitops-blueprint-aks) principles, where the **configuration** is stored in a Git repository and monitored by GitOps agents (e.g. [Flux](https://fluxcd.io/)) running on clusters in the **fleet**.

# What Problem Does Fleet Configuration Management Solve?

Fleet configuration management solves a problem that only becomes apparent when managing many workloads across many clusters. This next series of diagrams outlines the problem as the number of clusters and workloads scales up from a single workload and cluster, to many workloads across many clusters.

## One Workload, One Cluster

![diagram of one workload, one cluster](/img/fleet-configuration-management/one-workload-one-cluster.png)

The simplest scenario starts with one workload being deployed into one cluster. The workload’s Kubernetes manifests are committed into a GitHub repository that is monitored by the Flux GitOps agent. When a change is made to the workload’s Kubernetes manifests, that change is detected and applied automatically in the cluster.

## One Workload, Many Clusters

![diagram of one workload, many clusters](/img/fleet-configuration-management/one-workload-many-clusters.png)

For the workload to be deployed to a fleet of Kubernetes clusters, each cluster just needs to host its own Flux agent that is configured to watch the same set of Kubernetes manifests for the workload. When a change is made to the workload’s Kubernetes manifests, that change is detected and applied by each Flux agent independently. This enables one instance of the workload to be deployed into each cluster in the fleet.

However, it is usually the case that each instance of this workload needs to be configured differently. This is because clusters in a fleet will often serve different purposes, and configuration must be applied to reflect that. For example, different hostnames, workload scaling behaviors, or feature flags might need to be controlled separately for different clusters in the fleet.

This can be made possible by providing multiple configurations as part of the workload’s Kubernetes manifests and modifying the Flux agents to watch their own dedicated configurations.

![diagram of one workload, many configurations](/img/fleet-configuration-management/one-workload-many-configs.png)

This enables independent configuration for each instance of the workload at the cost of additional complexity for managing configuration manifests and Flux agents.

## Many Workloads, Many Clusters

![diagram of many workloads, many clusters](/img/fleet-configuration-management/many-workloads-many-clusters.png)

To support additional workloads, the same setup needs to be replicated. Each workload will have its own set of Kubernetes manifests that each contains configuration for each cluster in the fleet and each flux agent needs to know what configurations to watch.

While this works and can be manageable for a small number of workloads and clusters, **the complexity of manually creating workload configurations does not scale well as the number of workloads and clusters increases**. Each workload must provide a unique set of configurations for each cluster in the fleet, and each cluster must be configured to watch the appropriate manifests.

## The Role of a Configuration Management System

A fleet configuration management system is an automated system that aims to simplify the complexities of manually managing workload configurations for the fleet. These systems serve 3 purposes:

1. Controlling what workloads get deployed into what clusters
1. Composing and delivering configurations for workloads onto clusters
1. Observability for inspecting what workloads are deployed on what clusters

![configuration management system simplification](/img/fleet-configuration-management/configuration-management-system.png)

Configuration management systems provide automation to reduce the amount of manual configuration from multiplicative scaling to additive scaling by the number of workloads and clusters in the fleet.

This allows engineering and operations teams to easily scale the number of workloads and clusters. When a new workload or cluster is added, it only needs to be registered with the fleet configuration management system. The system will then automatically compose and deliver the appropriate configurations to the clusters in the fleet.

## Do I Need A Fleet Configuration Management System?

Registering workloads and clusters with a fleet configuration management system requires managing higher-level deployment abstractions and managing a system that operates on these abstractions to automate configuration delivery. This adds complexity to the system and requires additional operational overhead. It is important to weigh this cost against the cost of manually curating workload configurations in your system.

# Example Architecture With Kalypso

This is an example architecture that generalizes the architecture that we built in our recent customer engagement. For more details on getting started with Kalypso, see its [GitHub repositories](https://github.com/microsoft/kalypso?tab=readme-ov-file#referenced-repositories) and the following Microsoft Learn pages:

- [Workload management in a multi-cluster environment with GitOps](https://learn.microsoft.com/en-us/azure/azure-arc/kubernetes/conceptual-workload-management)
- [Explore workload management in a multi-cluster environment with GitOps](https://learn.microsoft.com/en-us/azure/azure-arc/kubernetes/workload-management)

## Personas

This architecture serves two personas: the **Application Engineer** and the **Platform Engineer**. There may be many different Application Engineers, one for each independent workload.

The **Application Engineer** owns and authors workloads that run within the fleet of Kubernetes clusters. Each workload has its own independent software development lifecycle that is defined and practiced by the Application Engineer.

The **Platform Engineer** owns and manages the Kalypso Fleet Configuration Management Cluster. This service facilitates the complexities of generating and distributing the appropriate configuration across the fleet for all workloads.

## Architecture Diagram

![architecture diagram](/img/fleet-configuration-management/fleet-management.png)

## System Workflow

1. Application Engineers author changes to their workloads independently via git contributions.
2. Continuous Integration flows run independently in response to changes to workload source code.
3. Continuous Integration (CI) automatically compiles and publishes artifacts for use in deployments. For example, Docker images are built and pushed into Azure Container Registry for fleet clusters to pull from.
4. Continuous Deployment (CD) automatically compiles and publishes GitOps manifests for deployment into fleet clusters. For example, Kubernetes yaml files might get rendered and pushed to the GitHub repositories monitored by Flux agents in the fleet.
5. Platform Engineers schedule workload deployments to the fleet and coordinate generating platform dependent workload configuration. This is done via git contributions to the Platform Control Plane repository.
6. The Kalypso Scheduler running inside the management cluster automatically compiles and publishes GitOps manifests for deployment into fleet clusters. These are Kubernetes yaml files monitored by Flux agents in the fleet.
7. Flux agents in the fleet detect any changes to GitOps manifests from workloads and the platform GitOps repository.
8. Flux agents interact with their respective Kubernetes API servers to ensure that resources are provisioned in the fleet.
9. The Observability Hub running inside the management cluster monitors deployment state for all workloads across the fleet. This information is surfaced in Grafana dashboards.
10. Engineers monitor workload deployments across the fleet using deployment observability dashboards.

## Kalypso Runbooks

To learn more about how Application Engineers and Platform Engineers use this system, see the [Kalypso Runbooks](https://github.com/microsoft/kalypso/tree/main/docs/run-books) in Kalypso's documentation. These runbooks provide step-by-step instructions on the common Kalypso use cases and include examples for each scenario.

# Conclusion

Fleet configuration management is a powerful tool for scaling configuration management for many different workloads across many different hosting platforms. These systems automate the tedious work of managing individual configuration files by using higher level abstractions to compose and deliver configuration to the fleet.

## Acknowledgements

Thank you to the rest of the ISE dev crew who helped with the customer engagement that inspired this post [Bindu Chinnasamy](https://www.linkedin.com/in/bindu-msft-cse/), [John Hauppa](https://www.linkedin.com/in/johnhauppa/), [Uffaz Nathaniel](https://www.linkedin.com/in/uffaz-nathaniel-85588935/), [Bryan Leighton](https://www.linkedin.com/in/bryan77/), [Laura Lund](https://www.linkedin.com/in/lundlaura/), and [Kanishk Tantia](https://www.linkedin.com/in/kanishk-t-1723b2107/).

And a special thank you to [Eugene Fedorenko](https://www.linkedin.com/in/eugene-fedorenko-661a2636/) for his expertise and guidance on using Kalypso.
