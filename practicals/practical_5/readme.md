# WEB303 Microservices & Serverless Applications

## Practical 5 Report: Refactoring Monolith to Microservices

**Student:** Kinley-pal8  
**Module:** WEB303 Microservices & Serverless Applications  
**Date:** October 9, 2025  
**GitHub Repository:** [https://github.com/Kinley-pal8/Web303_p5](https://github.com/Kinley-pal8/Web303_p5)

---

## Executive Summary

This report documents the successful refactoring of a monolithic Student Cafe application into a microservices architecture. The project demonstrates systematic service extraction using domain-driven design principles, resulting in four independent services (User, Menu, Order) orchestrated by an API Gateway. All services are containerized with Docker and orchestrated via Docker Compose, showcasing modern microservices patterns including database-per-service, inter-service communication, and centralized API routing.

---

## Learning Outcomes Addressed

- **LO1:** Identified characteristics, benefits, and trade-offs of monolithic vs. microservices architectures
- **LO2:** Applied domain-driven design principles to identify service boundaries
- **LO3:** Systematically extracted services from monolith while maintaining functionality
- **LO4:** Implemented service discovery patterns with inter-service communication
- **LO5:** Deployed and orchestrated multiple services using Docker Compose
- **LO6:** Understood migration paths toward advanced architectures

---

## Project Deliverables

### 1. **Monolithic Application (Phase 1)** 

- **Location:** `student-cafe-monolith/`
- **Port:** 8080
- **Database:** PostgreSQL (shared single database)
- **Features:** All-in-one application with user management, menu catalog, and order processing
- **Status:** Fully functional baseline implementation

### 2. **Microservices Implementation (Phases 2-4)** 

#### User Service

- **Port:** 8081 | **Database:** user_db (port 5434)
- **Endpoints:**
  - `POST /users` - Create user
  - `GET /users/{id}` - Get single user
  - `GET /users` - List all users
- **Status:** Complete with GORM models and handlers

#### Menu Service

- **Port:** 8082 | **Database:** menu_db (port 5433)
- **Endpoints:**
  - `POST /menu` - Create menu item
  - `GET /menu/{id}` - Get menu by ID
- **Models:** Menu, MenuItem with proper relationships
- **Status:** Complete with independent service structure

#### Order Service

- **Port:** 8083 | **Database:** order_db (port 5435)
- **Endpoints:**
  - `POST /orders` - Create order (with inter-service validation)
  - `GET /orders` - List all orders
- **Inter-Service Communication:**
  - Validates users via user-service
  - Retrieves menu items via menu-service
  - Snapshots prices at order time
- **Status:** Complete with HTTP-based service calls

### 3. **API Gateway (Phase 5)** 

- **Port:** 8080
- **Routing:**
  - `/api/users*` → user-service:8081
  - `/api/menu*` → menu-service:8082
  - `/api/orders*` → order-service:8083
- **Features:** Request path rewriting, reverse proxy pattern, centralized entry point
- **Status:** Complete with middleware and logging

### 4. **Docker & Orchestration** 

- **Root Docker Compose:** Orchestrates all services and databases
- **Dockerfiles:** Multi-stage builds with Alpine Linux for all services
- **Database Configuration:** Separate PostgreSQL instances for each service
- **Volumes:** Persistent data storage for each database
- **Status:** All services buildable and runnable

---

## Architecture Overview

```
┌─────────────────────┐
│   External Clients  │
└──────────┬──────────┘
           │
           ▼
   ┌───────────────────┐
   │   API Gateway     │
   │   (port 8080)     │
   └───┬───────┬───┬───┘
       │       │   │
    ┌──┴──┐ ┌─┴─┐ ┌┴──┐
    │ :81 │ │:82│ │:83│
    ▼     ▼     ▼
┌────────┬──────────┬───────┐
│ User   │  Menu    │ Order │
│ Service│ Service  │Service│
└──┬─────┴──┬───────┴───┬───┘
   │        │           │
   ▼        ▼           ▼
┌────┐  ┌────┐      ┌────┐
│user│  │menu│      │order│
│ db  │  │ db │      │ db │
└────┘  └────┘      └────┘
```

---

## Implementation Highlights

### Database-per-Service Pattern

- **User Service:** Owns user_db with User model
- **Menu Service:** Owns menu_db with Menu and MenuItem models
- **Order Service:** Owns order_db with Order and OrderItem models
- **Benefits:** Independent scaling, different storage strategies possible, reduced coupling

### Inter-Service Communication

Order service demonstrates HTTP-based communication:

```go
// Validate user existence
userResp, err := http.Get(fmt.Sprintf("%s/users/%d", userServiceURL, req.UserID))

// Retrieve menu item with price snapshot
menuResp, err := http.Get(fmt.Sprintf("%s/menu/%d", menuServiceURL, item.MenuItemID))
```

### Service Discovery

- Services reference each other via container names (DNS resolution)
- No hardcoded IP addresses
- Natural Docker Compose networking integration

---

## Testing and Validation

### Build Verification 

All services compile successfully:

```bash
go build ./...  # Verified for all 5 services
```

### Docker Build Verification [Complete]

All Dockerfiles build without errors using multi-stage compilation

### Functional Testing

Example curl commands to test via API Gateway:

```bash
# Create user
curl -X POST http://localhost:8080/api/users \
  -H "Content-Type: application/json" \
  -d '{"name": "John Doe", "email": "john@example.com"}'

# Create menu item
curl -X POST http://localhost:8080/api/menu \
  -H "Content-Type: application/json" \
  -d '{"name": "Coffee", "description": "Hot coffee", "price": 2.50}'

# Create order (demonstrates inter-service communication)
curl -X POST http://localhost:8080/api/orders \
  -H "Content-Type: application/json" \
  -d '{"user_id": 1, "items": [{"menu_item_id": 1, "quantity": 2}]}'

# Get all orders
curl http://localhost:8080/api/orders
```

---

## Deployment

### Quick Start

```bash
cd /home/easykp8/Desktop/Year\ 3/practical5
docker-compose up --build
```

### Service Access

- **API Gateway:** http://localhost:8080
- **User Service:** http://localhost:8081
- **Menu Service:** http://localhost:8082
- **Order Service:** http://localhost:8083

### Database Access (if needed)

- PostgreSQL user-db: localhost:5434
- PostgreSQL menu-db: localhost:5433
- PostgreSQL order-db: localhost:5435
- All use: `postgres` / `postgres`

---

## File Structure

```
practical5/
├── student-cafe-monolith/     [Complete] Monolithic baseline
│   ├── main.go
│   ├── models/ (user, menu, order)
│   ├── handlers/ (user, menu, order)
│   ├── database/
│   ├── Dockerfile
│   └── go.mod
├── user-service/               [Complete] Extracted user service
│   ├── main.go
│   ├── models/
│   ├── handlers/
│   ├── database/
│   ├── Dockerfile
│   └── go.mod
├── menu-service/               [Complete] Extracted menu service
│   ├── main.go
│   ├── models/
│   ├── handlers/
│   ├── database/
│   ├── Dockerfile
│   └── go.mod
├── order-service/              [Complete] Extracted order service
│   ├── main.go
│   ├── models/
│   ├── handlers/
│   ├── database/
│   ├── Dockerfile
│   └── go.mod
├── api-gateway/                [Complete] Request router
│   ├── main.go
│   ├── Dockerfile
│   └── go.mod
├── docker-compose.yml          [Complete] Complete orchestration
├── README.md                   [Complete] Comprehensive documentation
└── PRACTICAL5_REPORT.md        [Complete] This report
```

---

## Key Achievements

| Phase | Deliverable                            | Status   |
| ----- | -------------------------------------- | -------- |
| 1     | Monolithic baseline                    | Complete |
| 2     | Menu service extraction                | Complete |
| 3     | User service extraction                | Complete |
| 4     | Order service with inter-service calls | Complete |
| 5     | API Gateway with routing               | Complete |
| 6     | Docker Compose orchestration           | Complete |

---

## Production Considerations

### Current Implementation

- Database per service (data isolation)
- Independent deployment capability
- HTTP-based inter-service communication
- Docker containerization
- Service orchestration with Docker Compose

### Future Enhancements

- Consul integration for dynamic service discovery
- Health check endpoints and monitoring
- Circuit breaker pattern for resilience
- Kubernetes deployment
- API Gateway advanced features (caching, rate limiting)
- Distributed tracing with Jaeger

---

## Conclusion

Practical 5 successfully demonstrates the complete refactoring journey from a monolithic application to a microservices architecture. The implementation showcases:

- **Service Decomposition:** Clear service boundaries using domain-driven design
- **Independent Deployment:** Each service with its own codebase and database
- **Inter-Service Communication:** Working HTTP-based service calls with validation
- **Container Orchestration:** Docker Compose managing the complete system
- **Scalability:** Each service can scale independently

All learning outcomes have been achieved through hands-on implementation of real microservices patterns. The project provides a solid foundation for advanced topics including service mesh, Kubernetes deployment, and event-driven architectures.

---

**Submission Checklist:**

- Source code for all services
- Dockerfile for each service
- Docker Compose orchestration file
- Database initialization (via GORM auto-migration)
- README with setup and testing instructions
- This comprehensive report
- Evidence of successful compilation and orchestration

---
