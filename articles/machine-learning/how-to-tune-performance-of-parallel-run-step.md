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
The internal scripts of ParallelRunStep requires minor CPU and memory. In common, users can focus on CPU and memory usage of their own scripts.

### Network
ParallelRunStep requires a lot of network I/O operation to support dataset processing, mini-batch scheduling and processing. Bandwidth and latency are the primary concerns of network.

### Disk
Logs of ParallelRunStep are stored in temporary location of local disk which cost minor disk usage.
Under specific circumstances where dataset is consumed in "download" mode, users have to ensure computes have enough disk space to handle mini-batch. For example, there is a job where the size of each mini-match is 500 MB and the process_count_per_node is 4. If this job is running on Windows compute, where ParallelRunStep will cache each mini-batch to local disk by default, the minimum disk space should be 2000 MB.


## How To Choose Compute Target

For the concept of compute target, please refer to: 
- [What are compute targets in Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/concept-compute-target)

For the sizes and options for Azure virtual machines, please refer to: 
- [Sizes for virtual machines in Azure](https://docs.microsoft.com/azure/virtual-machines/sizes)


## How To Choose Mini-batch Size
Mini-batch size is passed to a single run() call in entry script. To investigate the performance of mini-batch processing, a detailed log can be found in `logs/sys/job_report/processed_mini-batch.csv`. There are three metrics which are helpful:
- Elapsed Seconds: The total duration of mini-batch processing.
- Process Seconds: The CPU time of mini-batch processing. This metric indicates the busyness of CPU.
- Run Method Seconds: The duration of run() in entry script.


## How To Choose Node Count
Node count determines the number of compute nodes to be used for running the user script. It should not exceed the maximum number of nodes of compute target. 
In general, more node counts can provide better parallelism and save more job running time. The number of mini-batches processed by each node can be found in `logs/sys/job_report/node_summary.csv`. If the report shows mini-batches allocation skews a lot among all nodes, a possible explanation is that the compute resource is more than sufficient for current job. User can consider reducing node count to save budget.


## How to Choose Process Count Per Node
The best practice is to set it to the core number of GPU or CPU on one node. If too many processes are used, the synchronization overhead will increase and will not save overall runtime.

