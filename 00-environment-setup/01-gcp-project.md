# GCP Project Setup

## Objective

Create a cloud environment for Platform Engineering labs.

## Platform

Google Cloud Platform

## VM Configuration

| Parameter | Value |
|-----------|-------|
| OS | Ubuntu 24.04.4 LTS |
| vCPU | 4 |
| RAM | 16 GB |
| Disk | 100 GB |
| Region | Asia South |
| Machine Type | e2-standard-4 |

## Verification

Commands executed:

```bash
hostnamectl
lsb_release -a
free -h
df -h
nproc
ip addr show
```

Environment verified successfully.
