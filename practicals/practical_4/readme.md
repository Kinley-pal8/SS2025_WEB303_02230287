# WEB303 Microservices & Serverless Applications

## Practical 4 Report

**Student:** Kinley Palden
**Module:** WEB303 Microservices & Serverless Applications  
**Date:** November 26, 2025  
**GitHub Repository:** [https://github.com/Kinley-pal8/Web303_p4](https://github.com/Kinley-pal8/Web303_p4)

---

## Executive Summary

This practical demonstrates cloud-native microservices architecture using Go, React, Kubernetes, and Kong API Gateway. Implementation showcases container orchestration, service discovery with Kubernetes DNS, and API gateway management for a student cafe ordering system.

---

## Learning Outcomes

- Identified Kubernetes deployment and orchestration principles
- Implemented microservices with container orchestration
- Applied service discovery using Kubernetes DNS
- Configured Kong API Gateway for traffic routing
- Integrated React frontend with backend services
- Deployed multi-service applications on Kubernetes

---

## Architecture

**Services:**

- **Food Catalog Service** (Port 8080) - Go service for menu items
- **Order Service** (Port 8081) - Go service for order management
- **Cafe UI** (Port 80) - React frontend application
- **Kong API Gateway** (Port 80/443) - Request routing and management

**Kubernetes Components:**

- Kubernetes DNS for service discovery
- Horizontal Pod Autoscaling (HPA)
- Health checks (readiness & liveness probes)
- Rolling updates for zero-downtime deployment

---

## Key Features

- **Kubernetes Orchestration**: Declarative deployment with YAML manifests
- **Service Discovery**: Kubernetes native DNS resolution
- **API Gateway**: Kong for unified external entry point
- **Frontend Integration**: React consuming microservices
- **Auto-scaling**: Horizontal pod autoscaling based on metrics
- **Health Monitoring**: Automatic health checks and failover

---

## Quick Start

```bash
# Start Minikube cluster
minikube start
eval $(minikube docker-env)

# Create namespace
kubectl create namespace student-cafe

# Deploy applications
kubectl apply -f app-deployment.yaml
kubectl apply -f kong-ingress.yaml

# Get access URL
minikube service kong-kong-proxy -n student-cafe --url
```

**Access Points:**

- Kong API Gateway: External LoadBalancer (see minikube output)
- Kubernetes Dashboard: `minikube dashboard`

---

## Testing

**Example API Calls:**

```bash
# Get menu items
curl http://localhost:8080/api/catalog/items

# Create order
curl -X POST http://localhost:8080/api/orders/orders \
  -H "Content-Type: application/json" \
  -d '{"item_ids":["1","2"]}'

# Health check
curl http://localhost:8080/api/catalog/health
```

---

## Conclusion

This practical successfully demonstrates cloud-native microservices deployment using Kubernetes orchestration, service discovery, and Kong API Gateway routing for a complete distributed system.
