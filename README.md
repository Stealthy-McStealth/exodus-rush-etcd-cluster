# etcd-cluster

Distributed key-value store for the Exodus Rush game. Provides distributed consensus using the Raft protocol.

## Overview

This etcd cluster serves as a distributed state store for the application. The sea-state-service needs to be configured to use this cluster for shared state management.

**Service Configuration:**
- **Port:** 2379 (client), 2380 (peer)
- **Replicas:** 3 (StatefulSet for high availability)
- **Access URL:** `http://etcd-cluster:2379` or `http://etcd-cluster-client:2379`

## Features

- Strong consistency guarantees via Raft consensus
- Distributed cluster with 3 nodes for high availability
- Used for distributed locks, configuration, and shared state
- Health endpoints for Kubernetes probes

## Deployment

```bash
# Create namespace
kubectl create namespace passover

# Deploy etcd cluster
kubectl apply -f k8s/statefulset.yaml
kubectl apply -f k8s/service.yaml

# Verify deployment
kubectl get pods -n passover -l app=etcd-cluster
kubectl get svc -n passover | grep etcd
```

## Usage

The etcd cluster is accessed by services via the client service endpoint:

```
http://etcd-cluster-client:2379
```

Or directly via headless service for individual nodes:

```
http://etcd-cluster-0.etcd-cluster:2379
http://etcd-cluster-1.etcd-cluster:2379
http://etcd-cluster-2.etcd-cluster:2379
```

## Health Check

```bash
# Check cluster health
kubectl exec -n passover etcd-cluster-0 -- etcdctl endpoint health

# Check cluster members
kubectl exec -n passover etcd-cluster-0 -- etcdctl member list
```

