---
id: migration
title: Migration
sidebar_label: Migration
---

<!--
WARNING: this file was automatically generated by Mia-Platform Doc Aggregator.
DO NOT MODIFY IT BY HAND.
Instead, modify the source file and run the aggregator to regenerate this file.
-->

This document provides information to migrate from a previous version of the service.

## 0.5.0

### CRUD collections

Add the following fields to the [detections CRUD collection][crud-detections]:

| Name               | Type              | Required (Yes/No) | Nullable (Yes/No) |
|--------------------|-------------------|-------------------|-------------------|
| thresholds         | `Array of object` | No                | No                |
| thresholdsExceeded | `Boolean`         | No                | No                |

## 0.4.0

### CRUD collections

- Add the following fields to the [detections CRUD collection][crud-detections]:

| Name     | Type     | Required (Yes/No) | Nullable (Yes/No) |
|----------|----------|-------------------|-------------------|
| deviceId | `String` | No                | No                |

- Add the following fields to the [monitorings CRUD collection][crud-monitorings]:

| Name            | Type              | Required (Yes/No) | Nullable (Yes/No) |
|-----------------|-------------------|-------------------|-------------------|
| assignedDevices | `Array of string` | No                | No                |


[crud-detections]: ./20_configuration.md#detections
[crud-monitorings]: 20_configuration.md#monitorings
