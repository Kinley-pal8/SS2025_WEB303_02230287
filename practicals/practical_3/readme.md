# WEB303 Microservices & Serverless Applications

## Practical 3 Report

**Student:** Kinley Palden 
**Module:** WEB303 Microservices & Serverless Applications  
**Date:** November 26, 2025  
**GitHub Repository:** [https://github.com/Kinley-pal8/Web303_p3](https://github.com/Kinley-pal8/Web303_p3)

---

## Executive Summary

This practical demonstrates microservices architecture patterns using Go, gRPC, PostgreSQL, and Consul service discovery. Implementation showcases service decomposition, database isolation, and containerized deployment.

---

## Learning Outcomes

- Identified characteristics of microservices vs monolithic architectures
- Applied domain-driven design principles for service boundaries
- Implemented inter-service communication with gRPC
- Deployed services using Docker and Docker Compose
- Integrated Consul for dynamic service discovery

---

## Architecture

**Services:**

- **API Gateway** (Port 8080) - REST endpoint routing
- **Users Service** (gRPC Port 50051) - User management
- **Products Service** (gRPC Port 50052) - Product catalog
- **Consul Registry** (Port 8500) - Service discovery

**Databases:**

- Users Database: PostgreSQL (Port 5432)
- Products Database: PostgreSQL (Port 5433)

---

## Key Features

- **Database-per-Service Pattern**: Independent data stores
- **Service Discovery**: Dynamic routing via Consul
- **gRPC Communication**: Type-safe inter-service calls
- **Docker Containerization**: Complete orchestration
- **Health Monitoring**: Automatic service health checks

---

## Quick Start

```bash
docker-compose up --build
```

**Access Points:**

- API Gateway: http://localhost:8080
- Consul UI: http://localhost:8500

---

## Testing

**Example API Calls:**

```bash
# Create user
curl -X POST http://localhost:8080/api/users \
  -H "Content-Type: application/json" \
  -d '{"name": "John Doe", "email": "john@example.com"}'

# Get user
curl http://localhost:8080/api/users/1

# Create product
curl -X POST http://localhost:8080/api/products \
  -H "Content-Type: application/json" \
  -d '{"name": "Laptop", "price": 999.99}'
```

---

## Conclusion

This practical successfully demonstrates core microservices principles including service decomposition, inter-service communication, and container orchestration using industry-standard tools and patterns.
