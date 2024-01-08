---
title: Azure Cost Efficiency
---
# Virtual Machines
- Ensure that the storage attached to a VM has a sensible size and tier, as especially premium storage can become very expensive. Use small disks and standard storage for VMs where storage is not critical.
- ...
# Kubernetes
- Use ARM-based virtual machines for node pools if the image architecture of the applications allows for it
- Enable ephemeral storage for virtual machines, as having attached storage is very expensive and unnecessary in the majority of cases (as storage is handled through Azure Storage anyways)
- ...
# Log Analytics
- Log analytics can become very expensive, especially when logs from, e.g., Kubernetes are ingested, as there can be hundreds of containers that do not even belong to own applications. Restrict the data that is logged to log analytics on the source services (if possible) or define a cap for logging if retention is not critical.
- ...
# Functions
- Use the [consumption plan](https://learn.microsoft.com/en-us/azure/azure-functions/consumption-plan) for real usage-based pricing. However, this is only possible if features restricted to the [premium plan](https://learn.microsoft.com/en-us/azure/azure-functions/functions-premium-plan) are not required (e.g., virtual network integration is not required and cold starts can be tolerated). The premium plan always has a minimum of one running instance (even if the application is unused), while the consumption plan causes no cost if the application is not in use.
- ...
# Container Apps
- Define a HTTP scaling rule that enables scale-to-zero (if possible). Otherwise, container apps become more expensive than containers/apps hosted with other approaches.
- ...
# Static Web Apps
- For simple applications and also less complex [[next-js]] applications, use Azure Static Web Apps, as they offer the cheapest way of hosting web applications.
- ...
# Bastion
Recommendation: Instead of Azure Bastion, use a self-made virtual machine ("Jumbox") within the virtual network that can be turned off/on when necessary (and only causes storage cost when turned off) -> Azure Bastion is very expensive, can only be deleted and not stopped, and only allows SSH access to virtual machines, but does not allow for access to databases.
# Storage Accounts
- ...
# App Service
Recommendation: Evaluate another dynamically scalable container-based hosting service, as app service is very expensive in terms of resources (e.g., container apps or container instances).