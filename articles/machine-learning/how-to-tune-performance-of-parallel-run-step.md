---
title: ParallelRunStep Performance Tuning Guide
titleSuffix: Azure Machine Learning
description: This guide describes how to optimize ParallelRunStep pipeline run.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: troubleshooting
ms.reviewer: jmartens, larryfr, vaidyas, laobri, tracych
ms.author: bi
author: bi
ms.date: 10/15/2020
---

# ParallelRunStep Performance Tuning Guide

## Introduction to Performance Report
The performance report is located in `logs/sys/perf/`. It consists of resource usage reports in several dimensions. All reports are grouped by node. Under folder of each node, the below files are included:

- `node_resource_usage.csv`: This file provides an overview of resource usage of a node.
- `node_disk_usage.csv`: This file provides detailed disk usage of a node.
- `processes_resource_usage.csv`: This file provides an overview of resource usage of each worker process in a node.


## Understanding ParallelRunStep Resource Requirements

### CPU and Memory
As the size of input data increases, the internal scripts of ParallelRunStep will consume more memory for data processing. 
The internal scripts of ParallelRunStep have low CPU requirements. The overall CPU usage should mainly depend on scripts of users.

### Network
ParallelRunStep requires a lot of network I/O operation to support dataset processing, mini-batch scheduling and processing. Bandwidth and latency are the primary concerns of network.

### Disk
Logs of ParallelRunStep are stored in temporary location of local disk during the job which cost minor disk usage. Under specific circumstances where dataset is consumed in "download" mode, users have to ensure computes have enough disk space to handle mini-batch.


## How to Choose Compute Target

For the concept of compute target, please refer to: 
- [What are compute targets in Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/concept-compute-target)

For the sizes and options for Azure virtual machines, please refer to: 
- [Sizes for virtual machines in Azure](https://docs.microsoft.com/azure/virtual-machines/sizes)


## Best Practices of Compute Target Choice
TBC.


## FAQ on Performance Issues
TBC.