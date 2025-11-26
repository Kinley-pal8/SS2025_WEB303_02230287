# WEB303 Microservices & Serverless Applications
## Practical 2 Report: API Gateway with Service Discovery

**Student:** Kinley Palden
**Module:** WEB303 Microservices & Serverless Applications  
**Date:** August 2025  
**GitHub Repository:** [https://github.com/Kinley-pal8/Web303_p2](https://github.com/Kinley-pal8/Web303_p2)

---

## Executive Summary

This report documents the successful implementation of a microservices ecosystem utilizing an API Gateway pattern with dynamic service discovery through HashiCorp Consul. The project demonstrates the creation of two independent microservices (Users Service and Products Service) that register themselves with a centralized service registry, enabling dynamic routing through a smart API Gateway.

The implementation showcases key microservices architecture patterns including service decoupling, centralized routing, health monitoring, and fault tolerance. All specified learning outcomes were achieved, particularly focusing on efficient inter-service communication patterns and observability solutions through distributed service management.

---

## 1. Introduction and Project Scope

### 1.1 Objective
This practical aimed to build a resilient microservices ecosystem where services operate independently while maintaining centralized discoverability. The core objective was to demonstrate how an API Gateway can dynamically route requests to healthy service instances without requiring manual configuration updates when services are added, removed, or restarted.

### 1.2 Learning Outcomes Addressed
The implementation directly supports the following module learning outcomes:
- **LO2:** Design and implement microservices using efficient inter-service communication patterns
- **LO8:** Implement observability solutions including distributed service monitoring, health checking, and request tracing

### 1.3 Architecture Philosophy
The project implements the "smart endpoint, dumb pipes" principle where:
- Services are autonomous and self-registering
- The API Gateway provides intelligent routing without being tightly coupled to service implementations
- Service discovery enables dynamic topology changes without system-wide reconfiguration

---

## 2. System Architecture and Design

### 2.1 High-Level Architecture

```
┌─────────────────┐
│   External      │
│   Clients       │
└─────────┬───────┘
          │ HTTP Requests
          ▼
┌─────────────────┐      ┌─────────────────┐
│   API Gateway   │◄────►│     Consul      │
│  (Port 8080)    │      │  Service Registry│
└─────────┬───────┘      │  (Port 8500)    │
          │              └─────────────────┘
          │ Service Discovery
          │ & Health Checks
          ▼
┌─────────────────┬─────────────────┐
│ Users Service   │ Products Service│
│  (Port 8081)    │  (Port 8082)    │
│                 │                 │
│ - User CRUD     │ - Product CRUD  │
│ - Health Checks │ - Health Checks │
│ - Self Registry │ - Self Registry │
└─────────────────┴─────────────────┘
```

### 2.2 Component Architecture

#### API Gateway Layer
- **Function:** Single entry point for all external requests
- **Responsibilities:** Request routing, service discovery, load balancing, request/response transformation
- **Technology:** Go with standard HTTP libraries and Consul API client
- **Port:** 8080 (externally accessible)

#### Service Discovery Layer  
- **Function:** Centralized service registry and health monitoring
- **Technology:** HashiCorp Consul in development mode
- **Features:** Service registration, health checking, service lookup API
- **Port:** 8500 (management UI and API)

#### Microservices Layer
- **Users Service:** Manages user-related operations on port 8081
- **Products Service:** Handles product-related operations on port 8082
- **Common Features:** Self-registration, health endpoints, independent deployment

### 2.3 Communication Patterns

#### Service Registration Flow
1. Service starts and initializes HTTP server
2. Service registers itself with Consul including health check endpoint
3. Consul begins periodic health monitoring (10-second intervals)
4. Service becomes discoverable by API Gateway

#### Request Routing Flow
1. Client sends request to API Gateway
2. Gateway extracts service identifier from URL path
3. Gateway queries Consul for healthy service instances
4. Gateway creates reverse proxy to selected service instance
5. Request forwarded to target service with path rewriting
6. Response returned through Gateway to client

---

## 3. Implementation Details

### 3.1 Development Environment Setup

**Prerequisites Installed:**
- Go 1.21+ for microservices development
- Docker Desktop for Consul containerization
- HashiCorp Consul for service discovery
- Required Go dependencies: Chi router and Consul API client

**Project Structure Established:**
```
go-microservices/
├── users-service/
│   ├── main.go              # User service implementation
│   ├── go.mod               # Module dependencies
│   └── go.sum               # Dependency checksums
├── products-service/
│   ├── main.go              # Product service implementation
│   ├── go.mod               # Module dependencies  
│   └── go.sum               # Dependency checksums
├── api-gateway/
│   ├── main.go              # Gateway implementation
│   ├── go.mod               # Module dependencies
│   └── go.sum               # Dependency checksums
├── screenshots/
│   ├── consul-ui.png        # Consul dashboard evidence
│   ├── curl-test.png        # API testing evidence
│   └── terminal-output.png  # Gateway routing logs
└── README.md                # Project documentation
```

### 3.2 Service Implementation Strategy

#### Users Service Architecture
The Users Service implements a standard HTTP REST pattern with the following components:

**Core Functionality:**
- RESTful endpoint: `GET /users/{id}`
- Health check endpoint: `GET /health`
- Consul registration with unique service instance ID
- Automatic health monitoring configuration

**Key Implementation Features:**
```go
// Service constants
const serviceName = "users-service"
const servicePort = 8081

// Consul registration with health checks
registration := &consulapi.AgentServiceRegistration{
    ID:      fmt.Sprintf("%s-%s", serviceName, hostname),
    Name:    serviceName,
    Port:    servicePort,
    Address: hostname,
    Check: &consulapi.AgentServiceCheck{
        HTTP:     fmt.Sprintf("http://%s:%d/health", hostname, servicePort),
        Interval: "10s",
        Timeout:  "1s",
    },
}
```

#### Products Service Architecture  
The Products Service follows an identical pattern to Users Service with differentiated:
- Service name: `products-service`
- Port allocation: 8082
- Endpoint pattern: `GET /products/{id}`
- Independent module and dependency management

This design demonstrates the reusable nature of microservices patterns and the ease of horizontal scaling.

#### API Gateway Implementation
The Gateway implements dynamic service discovery and request routing:

**Routing Logic:**
- URL pattern matching: `/api/{service}/{resource}`
- Service name extraction and transformation
- Dynamic Consul service lookup
- Reverse proxy creation and request forwarding

**Service Discovery Integration:**
```go
// Dynamic service discovery
func discoverService(name string) (*url.URL, error) {
    services, _, err := consul.Health().Service(name, "", true, nil)
    if len(services) == 0 {
        return nil, fmt.Errorf("no healthy instances found")
    }
    // Load balancing: select first healthy instance
    service := services[0].Service
    return url.Parse(fmt.Sprintf("http://%s:%d", service.Address, service.Port))
}
```

### 3.3 Service Discovery and Health Monitoring

#### Consul Configuration
- **Deployment Mode:** Development mode for simplified setup
- **Health Check Frequency:** 10-second intervals with 1-second timeout
- **Service Registration:** Automatic on service startup
- **Health Endpoints:** `/health` on each service

#### Health Check Implementation
Each service exposes a standardized health endpoint:
```go
func healthCheckHandler(w http.ResponseWriter, r *http.Request) {
    w.WriteHeader(http.StatusOK)
    fmt.Fprintln(w, "Service is healthy")
}
```

This enables Consul to continuously monitor service availability and automatically update the service registry when instances become unhealthy.

---

## 4. Testing and Validation

### 4.1 System Deployment Testing

**Multi-Terminal Deployment Process:**
1. **Terminal 1:** Consul agent initialization
2. **Terminal 2:** Users Service startup and registration
3. **Terminal 3:** Products Service startup and registration  
4. **Terminal 4:** API Gateway initialization

**Service Registration Verification:**
- Consul UI accessed at `http://localhost:8500`
- Both services visible with healthy status indicators
- Health check intervals functioning correctly

### 4.2 Functional Testing Results

#### Endpoint Testing via cURL

**Users Service Test:**
```bash
curl http://localhost:8080/api/users/123
# Expected: "Response from 'users-service': Details for user 123"
# Actual: Successfully routed and responded
```

**Products Service Test:**
```bash
curl http://localhost:8080/api/products/abc  
# Expected: "Response from 'products-service': Details for product abc"
# Actual: Successfully routed and responded
```

#### API Gateway Routing Logs
Gateway terminal output confirmed:
- Request reception and path parsing
- Successful service discovery via Consul
- Correct URL rewriting and request forwarding
- Response relay to client

### 4.3 Resilience and Fault Tolerance Testing

#### Service Failure Simulation
**Test Procedure:**
1. Stopped Users Service (Ctrl+C)
2. Monitored Consul UI for health status change (≈10 seconds)
3. Attempted request to Users Service via Gateway
4. Verified Products Service continued operation
5. Restarted Users Service
6. Confirmed automatic re-registration and recovery

**Results:**
- Health checks correctly identified failed service
- Gateway returned appropriate error for unavailable service
- Other services remained unaffected
- Service recovery was automatic and seamless
- No manual reconfiguration required

### 4.4 Evidence Documentation

#### Screenshot Evidence Captured:
1. **Consul UI Dashboard:** Showing both services registered and healthy
2. **cURL Testing Results:** Demonstrating successful API responses
3. **Gateway Terminal Output:** Displaying request routing and service discovery logs

---

## 5. Challenges and Solutions

### 5.1 Network Connectivity Issues

**Challenge:** Initial service registration failures between containerized Consul and host-based services

**Root Cause Analysis:** Services running on host machine could not communicate with Consul instance running in Docker container due to network isolation

**Solutions Implemented:**
1. **Option 1:** Installed Consul locally on host machine
   - Downloaded Consul binary from HashiCorp
   - Ran `consul agent -dev` for development mode
   - Eliminated container-host network barriers

2. **Option 2:** Full containerization approach (recommended for production)
   - Created Dockerfiles for all services
   - Implemented Docker Compose orchestration
   - Established shared Docker network for service communication

**Selected Approach:** Option 1 for simplicity in development environment

### 5.2 Service Discovery Configuration

**Challenge:** Proper hostname resolution and health check endpoint configuration

**Solution:**
- Used `os.Hostname()` for dynamic address resolution
- Configured health check URLs to match actual service binding
- Implemented proper error handling for registration failures

### 5.3 Request Routing Logic

**Challenge:** URL path rewriting and service name mapping

**Solution:**
- Implemented path segment parsing for service identification
- Created dynamic URL rewriting: `/api/users/123` → `/users/123`
- Added comprehensive logging for debugging routing issues

---

## 6. Learning Outcomes and Technical Insights

### 6.1 Microservices Architecture Understanding

**Key Insights Gained:**
- **Service Autonomy:** Each service operates independently with its own lifecycle
- **Decentralized Governance:** Services can be developed and deployed independently
- **Failure Isolation:** Individual service failures don't cascade to other services
- **Technology Diversity:** Each service can use different technology stacks

### 6.2 Service Discovery Patterns

**Technical Learning:**
- **Dynamic Service Registration:** Services self-register without external configuration
- **Health Monitoring:** Continuous health checking enables automatic failover
- **Load Balancing:** Service discovery enables multiple instance management
- **Configuration Management:** Centralized service metadata storage

### 6.3 API Gateway Patterns

**Architectural Benefits:**
- **Single Entry Point:** Simplifies client-side service consumption
- **Cross-Cutting Concerns:** Centralized authentication, logging, monitoring
- **Service Aggregation:** Potential for request/response transformation
- **Version Management:** Enables service versioning strategies

### 6.4 Observability Implementation

**Monitoring Capabilities:**
- **Health Status Visibility:** Real-time service health through Consul UI
- **Request Tracing:** Gateway logs provide request flow visibility
- **Service Registry Monitoring:** Dynamic service topology observation
- **Failure Detection:** Automated unhealthy service identification

---

## 7. Production Readiness Considerations

### 7.1 Scalability Enhancements

**Horizontal Scaling:**
- Multiple service instances with load balancing
- Database per service pattern implementation
- Caching strategies for improved performance
- Auto-scaling based on demand metrics

**Vertical Scaling:**
- Resource optimization and profiling
- Connection pooling and resource management
- Memory and CPU usage monitoring
- Performance bottleneck identification

### 7.2 Security Implementation

**Authentication and Authorization:**
- JWT token validation in API Gateway
- Service-to-service authentication
- API rate limiting and throttling
- Input validation and sanitization

**Network Security:**
- TLS/SSL encryption for all communications
- Service mesh implementation for secure inter-service communication
- Network segmentation and firewall rules
- Secrets management for sensitive configuration

### 7.3 Operational Excellence

**Monitoring and Alerting:**
- Prometheus metrics collection
- Grafana dashboards for visualization
- Alert manager for critical event notification
- Log aggregation with ELK stack

**Deployment Strategies:**
- Blue-green deployments for zero-downtime updates
- Canary releases for gradual rollouts
- CI/CD pipeline integration
- Automated testing and quality gates

---

## 8. Future Enhancement Opportunities

### 8.1 Advanced Gateway Features

**Enhanced Routing:**
- Advanced load balancing algorithms (round-robin, weighted, least connections)
- Circuit breaker pattern implementation
- Request/response transformation capabilities
- API versioning and backward compatibility

**Observability Enhancement:**
- Distributed tracing with Jaeger or Zipkin
- Custom metrics collection and analysis
- Request correlation ID propagation
- Performance monitoring and SLA tracking

### 8.2 Service Mesh Integration

**Technology Adoption:**
- Istio or Linkerd service mesh implementation
- Envoy proxy sidecar pattern
- Advanced traffic management policies
- Enhanced security with mutual TLS

### 8.3 Cloud-Native Evolution

**Container Orchestration:**
- Kubernetes deployment with service mesh
- Helm charts for service packaging
- Custom resource definitions for service configuration
- GitOps deployment workflows

**Serverless Integration:**
- Function-as-a-Service integration
- Event-driven architecture patterns
- Hybrid serverless-container architectures
- Cost optimization through serverless scaling

---

## 9. Conclusion

This practical successfully demonstrated the implementation of a robust microservices architecture utilizing API Gateway and service discovery patterns. The project achieved all specified learning outcomes by creating a dynamic, resilient system where services can be independently developed, deployed, and scaled without requiring system-wide reconfiguration.

### 9.1 Key Achievements

**Technical Implementation:**
- Successfully built two independent microservices in Go
- Implemented centralized service discovery using HashiCorp Consul
- Created intelligent API Gateway with dynamic routing capabilities
- Demonstrated fault tolerance and automatic service recovery

**Architectural Understanding:**
- Practical experience with microservices communication patterns
- Understanding of service discovery and registration mechanisms
- Implementation of observability through health monitoring
- Hands-on experience with containerization and orchestration concepts

### 9.2 Educational Value

The practical provided valuable insights into real-world microservices challenges including service communication, failure handling, and operational complexity. The implementation demonstrates how proper architectural patterns can create systems that are both resilient and maintainable.

### 9.3 Foundation for Advanced Concepts

This implementation serves as a solid foundation for exploring more advanced microservices concepts including:
- Container orchestration with Kubernetes
- Service mesh architectures
- Advanced observability and monitoring
- Cloud-native deployment patterns

The project repository maintains comprehensive documentation and evidence, demonstrating professional development practices suitable for production environments and serving as a reference implementation for microservices architecture patterns.

---

## Evidence and Documentation

### Screenshot Evidence
1. **Consul UI Dashboard:** 
![Consul UI showing registered services](./screenshots/consul-ui.png)
 - Showing both services registered and healthy


2. **API Testing Results:** 
![Testing endpoints with cURL](./screenshots/curl-test.png) 
- Demonstrating successful endpoint responses  


3. **Gateway Routing Logs:** 
![API Gateway routing requests](./screenshots/terminal-output.png)
 - Displaying request routing and service discovery

### Repository Information
- **Complete Source Code:** Available at [https://github.com/Kinley-pal8/Web303_p2](https://github.com/Kinley-pal8/Web303_p2)
- **Documentation:** Comprehensive README with setup and testing instructions
- **Configuration Files:** All necessary configuration and deployment files included

---

## References and Technical Resources

- **Go Programming Language:** https://go.dev/
- **HashiCorp Consul:** https://www.consul.io/docs
- **Chi HTTP Router:** https://github.com/go-chi/chi
- **Microservices Patterns:** Martin Fowler's Microservices Architecture
- **Docker Documentation:** https://docs.docker.com/
- **API Gateway Patterns:** Cloud Architecture Patterns

---

*This report represents the completion of WEB303 Practical 2, demonstrating advanced microservices architecture implementation with service discovery, API gateway patterns, and comprehensive observability solutions.*