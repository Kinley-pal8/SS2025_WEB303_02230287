# WEB303 Microservices & Serverless Applications
## Practical 1 Report: From Foundational Setup to Inter-Service Communication

**Student:** Kinley-pal8  
**Module:** WEB303 Microservices & Serverless Applications  
**Date:** August 2025  
**GitHub Repository:** [https://github.com/Kinley-pal8/Web303_p1](https://github.com/Kinley-pal8/Web303_p1)

---

## Executive Summary

This report documents the successful implementation of a multi-container microservice application as part of WEB303 Practical 1. The project involved setting up a complete development environment and building two communicating microservices using Go, gRPC, Protocol Buffers, and Docker. The implementation demonstrates foundational concepts of microservices architecture, inter-service communication, and containerization strategies.

The practical achieved all specified learning outcomes, including the design and implementation of microservices using gRPC for efficient communication, containerization with Docker, and foundational understanding of microservices orchestration principles.

---

## 1. Introduction and Objectives

### 1.1 Project Scope
This practical focused on creating a foundational microservices environment supporting two primary objectives:
- **Part 1:** Establishing a complete development environment with Go, Protocol Buffers, and Docker
- **Part 2:** Building and orchestrating two communicating microservices using gRPC

### 1.2 Learning Outcomes Addressed
The implementation directly supports the following module learning outcomes:
- **LO2:** Design and implement microservices using gRPC and Protocol Buffers for efficient inter-service communication
- **LO6:** Deploy microservices using containerization technologies (foundational step toward Kubernetes)
- **LO1:** Demonstrate understanding of microservices architecture concepts and trade-offs

### 1.3 Technology Stack
- **Programming Language:** Go 1.24.2
- **Communication Protocol:** gRPC with Protocol Buffers
- **Containerization:** Docker with Docker Compose
- **Development Tools:** protoc compiler, grpcurl for testing

---

## 2. System Architecture

### 2.1 Service Architecture Overview
The implemented system consists of two distinct microservices operating within a containerized environment:

```
┌─────────────────┐    gRPC Call    ┌─────────────────┐
│                 │ ──────────────> │                 │
│ Greeter Service │                 │  Time Service   │
│   (Port 50051)  │ <────────────── │   (Port 50052)  │
│                 │    Response     │                 │
└─────────────────┘                 └─────────────────┘
        │                                   │
        └───────────── Docker Network ──────┘
```

### 2.2 Service Definitions

#### Time Service
- **Purpose:** Provides current timestamp functionality
- **Endpoint:** `GetTime(TimeRequest) returns (TimeResponse)`
- **Port:** 50052
- **Dependencies:** None (foundational service)

#### Greeter Service
- **Purpose:** Generates personalized greetings with timestamps
- **Endpoint:** `SayHello(HelloRequest) returns (HelloResponse)`
- **Port:** 50051 (externally accessible)
- **Dependencies:** Time Service (gRPC client)

### 2.3 Protocol Buffer Contracts

The services communicate through well-defined Protocol Buffer schemas:

**Time Service Contract (`time.proto`):**
- Simple request-response pattern
- Returns RFC3339 formatted timestamps
- Language-agnostic serialization

**Greeter Service Contract (`greeter.proto`):**
- Accepts name parameter
- Returns formatted greeting message
- Integrates time data from dependent service

---

## 3. Implementation Details

### 3.1 Development Environment Setup

**Go Installation and Configuration:**
- Successfully installed Go 1.24.2
- Configured GOPATH and environment variables
- Verified installation with `go version` and `go env` commands

**Protocol Buffers Toolchain:**
- Installed `protoc` compiler for schema compilation
- Configured Go-specific plugins: `protoc-gen-go` and `protoc-gen-go-grpc`
- Successfully generated Go code from `.proto` definitions

**Docker Environment:**
- Installed Docker Desktop
- Verified functionality with `hello-world` container
- Confirmed container orchestration capabilities

### 3.2 Service Implementation

#### Time Service Implementation
The Time Service implements a straightforward gRPC server pattern:
- Uses Go's `net` package for TCP listening on port 50052
- Implements the `TimeServiceServer` interface
- Returns current time using Go's `time.RFC3339` format
- Includes comprehensive logging for request tracking

#### Greeter Service Implementation
The Greeter Service demonstrates both server and client capabilities:
- Acts as gRPC server for external requests (port 50051)
- Functions as gRPC client to Time Service
- Implements service composition by combining greeting logic with time data
- Uses Docker's internal DNS resolution (`time-service:50052`)

### 3.3 Containerization Strategy

**Multi-stage Docker Builds:**
Both services utilize optimized multi-stage Dockerfiles:
- **Builder stage:** Uses `golang:1.24.2-alpine` for compilation
- **Runtime stage:** Uses minimal `alpine:latest` for deployment
- Significantly reduces final image size
- Ensures security through minimal attack surface

**Docker Compose Orchestration:**
The `docker-compose.yml` configuration provides:
- Automatic service discovery through hostname resolution
- Dependency management (`greeter-service` depends on `time-service`)
- Network isolation with internal communication
- External port mapping for client access

---

## 4. Project Structure and Organization

```
practical_1/
├── docker-compose.yml              # Orchestration configuration
├── greeter-service/
│   ├── Dockerfile                  # Greeter service container definition
│   ├── go.mod                      # Go module dependencies
│   ├── go.sum                      # Dependency checksums
│   └── main.go                     # Greeter service implementation
├── proto/
│   ├── greeter.proto              # Greeter service schema
│   ├── time.proto                 # Time service schema
│   └── gen/proto/                 # Generated Go code
│       ├── greeter_grpc.pb.go     # gRPC server/client code
│       ├── greeter.pb.go          # Message serialization
│       ├── time_grpc.pb.go        # gRPC server/client code
│       └── time.pb.go             # Message serialization
└── time-service/
    ├── Dockerfile                 # Time service container definition
    ├── go.mod                     # Go module dependencies
    ├── go.sum                     # Dependency checksums
    └── main.go                    # Time service implementation
```

---

## 5. Testing and Validation

### 5.1 Deployment Testing
**Container Build Process:**
```bash
docker compose up --build
```
Successfully built and started both containers with proper service discovery and network communication.


### 5.3 Service Logs Analysis
Container logs demonstrated:
- Successful service startup on designated ports
- Proper gRPC client connection establishment
- Request processing and inter-service communication
- No error conditions or connection failures

---

## 6. Challenges and Solutions

### 6.1 Development Environment Configuration
**Challenge:** Initial Protocol Buffer toolchain setup complexity
**Solution:** Systematic installation of `protoc` compiler and Go-specific plugins with proper PATH configuration

### 6.2 Docker Networking
**Challenge:** Understanding service discovery within Docker Compose networks
**Solution:** Utilized Docker Compose's automatic hostname resolution (`time-service:50052`) rather than localhost addressing

### 6.3 Multi-stage Docker Builds
**Challenge:** Optimizing container size while maintaining functionality
**Solution:** Implemented multi-stage builds separating compilation and runtime environments

---

## 7. Learning Outcomes and Insights

### 7.1 Technical Skills Developed
- Proficiency in Go programming for microservices development
- Understanding of gRPC and Protocol Buffers for service communication
- Practical experience with Docker containerization and orchestration
- Knowledge of microservices architecture patterns

### 7.2 Architectural Understanding
- Appreciation for service separation and bounded contexts
- Understanding of inter-service communication patterns
- Recognition of containerization benefits for deployment consistency
- Insight into service discovery mechanisms

### 7.3 Development Best Practices
- Importance of well-defined service contracts
- Value of comprehensive logging for debugging
- Benefits of multi-stage Docker builds for optimization
- Necessity of proper dependency management

---

## 8. Future Enhancements

### 8.1 Production Readiness
- Implementation of health checks for service monitoring
- Addition of metrics and observability tooling
- Configuration of proper logging aggregation
- Integration of security measures (TLS, authentication)

### 8.2 Scalability Improvements
- Horizontal scaling considerations for high availability
- Load balancing strategies for multiple service instances
- Database integration for persistent data storage
- Caching mechanisms for improved performance

### 8.3 Advanced Features
- Service mesh integration (Istio, Linkerd)
- Circuit breaker patterns for fault tolerance
- Distributed tracing for request flow analysis
- Configuration management and service discovery

---

## 9. Conclusion

This practical successfully demonstrated the fundamental concepts of microservices architecture through hands-on implementation. The project achieved all specified learning outcomes by creating two communicating services using industry-standard technologies including Go, gRPC, Protocol Buffers, and Docker.

The implementation showcases essential microservices patterns including service separation, contract-first design, containerization, and orchestration. The use of gRPC provides efficient, strongly-typed communication between services, while Docker ensures consistent deployment across environments.

The foundational knowledge and practical experience gained through this exercise provides a solid base for advancing to more complex microservices concepts including Kubernetes orchestration, service mesh architectures, and serverless deployment patterns in subsequent practicals.

The project repository maintains a clean, well-organized structure suitable for future development and serves as a reference implementation for microservices best practices using the Go ecosystem.

---

## References and Resources

- **Go Programming Language:** https://go.dev/
- **gRPC Documentation:** https://grpc.io/docs/
- **Protocol Buffers:** https://protobuf.dev/
- **Docker Documentation:** https://docs.docker.com/
- **Project Repository:** https://github.com/Kinley-pal8/Web303_p1

---

*This report represents the completion of WEB303 Practical 1, demonstrating foundational microservices implementation skills and establishing the groundwork for advanced distributed systems development.*