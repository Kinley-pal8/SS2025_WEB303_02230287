# WEB303: Microservices & Serverless Applications

Welcome to the practical repository for **WEB303: Microservices & Serverless Applications** (SS2025). This repository contains all hands-on practical work for the module, demonstrating real-world implementation of microservices and serverless architecture patterns.

**Student:** Kinley-pal8  
**Module Code:** WEB303  
**Semester:** Spring/Summer 2025  

---

## 📖 Module Overview

### **General Objectives**

This module introduces students to the principles, design patterns, and implementation techniques of microservices and serverless architectures. Students gain hands-on experience in developing, deploying, and managing distributed systems using modern tools and cloud-native technologies.

### **Key Learning Outcomes**

By completion of this module, students will be able to:

1. Explain fundamental concepts of microservices and serverless architectures
2. Design and implement microservices using gRPC and Protocol Buffers
3. Apply hexagonal architecture principles for loosely coupled services
4. Implement resilience patterns (timeout, retry, circuit breaker)
5. Develop comprehensive testing strategies for microservices
6. Deploy microservices to Kubernetes with various strategies
7. Design and implement serverless applications
8. Implement observability solutions (tracing, metrics, logging)
9. Evaluate patterns for data consistency in distributed systems
10. Optimize performance and address common bottlenecks

---

## 📂 Repository Structure

```
SS2025_WEB303_02230287/
├── README.md                           # This file
├── practicals/                         # All practical sessions
│   ├── practical_1/                    # Dev environment setup (Go, gRPC, Docker)
│   │   └── readme.md
│   ├── practical_2/                    # Basic gRPC microservice
│   │   └── readme.md
│   ├── practical_3/                    # Resilience patterns & service discovery
│   │   └── readme.md
│   ├── practical_4/                    # Kubernetes orchestration
│   │   └── readme.md
│   ├── practical_5/                    # Monolith to microservices refactoring
│   │   └── readme.md
│   ├── practical_5a/                   # Advanced microservices patterns
│   │   └── readme.md
│   ├── practical_5b/                   # Event-driven architecture
│   │   └── readme.md
│   └── practical_6/                    # Serverless functions
│       └── readme.md
└── [Other course materials]

```

---

## 🎯 Practicals Overview

### **Practical 1: Development Environment Setup**

- **Focus:** Development environment configuration
- **Technologies:** Go, gRPC, Docker
- **Deliverables:** Working development environment
- **[View Details](./practicals/practical_1/readme.md)**

### **Practical 2: Basic gRPC Microservice**

- **Focus:** Introduction to gRPC and Protocol Buffers
- **Technologies:** Go, gRPC, Protocol Buffers
- **Deliverables:** Functional gRPC service with client
- **[View Details](./practicals/practical_2/readme.md)**

### **Practical 3: Resilience Patterns & Service Discovery**

- **Focus:** Implementing resilience and service discovery
- **Technologies:** Go, gRPC, Consul, Docker Compose
- **Patterns:** Retry, timeout, circuit breaker, service discovery
- **[View Details](./practicals/practical_3/readme.md)**

### **Practical 4: Kubernetes Orchestration**

- **Focus:** Container orchestration and deployment
- **Technologies:** Kubernetes, Docker, Kong API Gateway, React
- **Patterns:** Service discovery, API gateway, health checks
- **[View Details](./practicals/practical_4/readme.md)**

### **Practical 5: Monolith to Microservices Refactoring**

- **Focus:** Systematic service extraction and decomposition
- **Technologies:** Go, PostgreSQL, Docker Compose, API Gateway
- **Patterns:** Database-per-service, inter-service communication
- **[View Details](./practicals/practical_5/readme.md)**

### **Practical 5a: Advanced Microservices Patterns**

- **Focus:** Advanced architectural patterns
- **Technologies:** Go, distributed systems patterns
- **Patterns:** Saga, event sourcing, CQRS
- **[View Details](./practicals/practical_5a/readme.md)**

### **Practical 5b: Event-Driven Architecture**

- **Focus:** Asynchronous communication and event streaming
- **Technologies:** Go, message queues, event streaming
- **Patterns:** Publish-subscribe, event-driven workflows
- **[View Details](./practicals/practical_5b/readme.md)**

### **Practical 6: Serverless Functions**

- **Focus:** Serverless computing and function-as-a-service
- **Technologies:** Serverless framework, cloud functions
- **Patterns:** Event-driven functions, API gateway integration
- **[View Details](./practicals/practical_6/readme.md)**

---

## 🛠️ Technology Stack

### **Core Technologies**

| Component             | Technology            | Version |
| --------------------- | --------------------- | ------- |
| **Runtime**           | Go                    | 1.18+   |
| **Containerization**  | Docker                | 20.10+  |
| **Orchestration**     | Kubernetes            | 1.23+   |
| **Message Protocol**  | gRPC                  | Latest  |
| **IDL**               | Protocol Buffers      | Proto 3 |
| **API Gateway**       | Kong                  | Latest  |
| **Frontend**          | React                 | 19.1+   |
| **Database**          | PostgreSQL            | 13+     |
| **Service Discovery** | Consul/Kubernetes DNS | Latest  |

### **Development Tools**

- **Go Modules** for dependency management
- **Docker Compose** for local orchestration
- **Minikube** for local Kubernetes cluster
- **kubectl** for cluster management
- **Protocol Buffer Compiler** (protoc) for code generation

---

## ⚙️ Quick Start

### **Prerequisites**

Ensure you have the following installed:

```bash
# Required tools
go --version              # Go 1.18 or higher
docker --version          # Docker 20.10 or higher
docker-compose --version  # Docker Compose 1.29 or higher
kubectl --version         # Kubernetes CLI
minikube --version        # Minikube for local K8s
protoc --version          # Protocol Buffers Compiler
```

### **Clone and Navigate**

```bash
git clone https://github.com/Kinley-pal8/SS2025_WEB303_02230287.git
cd SS2025_WEB303_02230287
```

### **Run a Practical**

```bash
# Navigate to a practical directory
cd practicals/practical_3

# Follow the specific practical README
cat readme.md
```

---

## 📊 Assessment Structure

Your final grade is determined by Continuous Assessment (CA):

| Component               | Weighting |
| ----------------------- | --------- |
| Practical Work & Report | 20%       |
| Mid-Term Test           | 20%       |
| Programming Assignments | 20%       |
| Final Project           | 40%       |
| **Total**               | **100%**  |

---

## 📚 Key Concepts Covered

### **Microservices Patterns**

- API Gateway Pattern
- Database-per-Service
- Service Discovery
- Circuit Breaker
- Saga Pattern
- Event Sourcing
- CQRS

### **Resilience Patterns**

- Timeout
- Retry
- Fallback
- Circuit Breaker
- Bulkhead

### **Communication Patterns**

- Synchronous: REST, gRPC
- Asynchronous: Message Queues, Event Streaming
- Service Mesh

### **Deployment Patterns**

- Rolling Updates
- Blue-Green Deployment
- Canary Release
- Feature Toggles

---

## 🔗 Useful Resources

### **Essential Reading**

- Newman, S. (2019). _Building microservices: Designing Fine-Grained Systems_. O'Reilly Media.
- Bellemare, A. (2020). _Building event-driven microservices_.
- Babal, H. (2023). _GRPC Microservices in Go_. Simon and Schuster.
- Anderson, E. (2023). _Building serverless applications on Knative_.

### **Online Documentation**

- [gRPC Documentation](https://grpc.io/docs/)
- [Protocol Buffers Guide](https://developers.google.com/protocol-buffers)
- [Kubernetes Documentation](https://kubernetes.io/docs/)
- [Docker Documentation](https://docs.docker.com/)
- [Kong API Gateway](https://docs.konghq.com/)

### **Tools & Platforms**

- [Go Official Site](https://golang.org/)
- [Docker Hub](https://hub.docker.com/)
- [GitHub](https://github.com/)

---

## 📝 Submission Guidelines

### **For Each Practical**

1. **Complete the implementation** as specified in the practical requirements
2. **Document your work** in the practical README with:
   - Brief overview of what was accomplished
   - Screenshots of successful execution
   - How to run/test your implementation
3. **Include evidence** of working code (screenshots, logs)
4. **Push to GitHub** regularly with meaningful commit messages
5. **Maintain clean code** following Go conventions

### **Commit Message Format**

```
practical_X: Brief description of changes

- Detailed explanation of implementation
- Any important notes or decisions
```

---

## 🔄 Getting Help

### **During Practicals**

- Review the specific practical's README.md
- Check provided code examples and documentation
- Refer to the module's learning materials

### **Common Issues**

**Docker connectivity issues?**

```bash
docker ps  # Verify Docker is running
docker-compose version  # Check Docker Compose
```

**Go module problems?**

```bash
go mod tidy          # Clean dependencies
go mod download      # Download dependencies
```

**Kubernetes cluster issues?**

```bash
minikube status      # Check cluster status
kubectl cluster-info # Verify connectivity
```

---

## 📈 Learning Path

```
Practical 1: Setup Environment
    ↓
Practical 2: Basic gRPC Service
    ↓
Practical 3: Resilience & Discovery
    ↓
Practical 4: Kubernetes
    ↓
Practical 5: Refactoring Monolith
    ↓
Practical 5a/5b: Advanced Patterns
    ↓
Practical 6: Serverless
```

Each practical builds upon the concepts from previous ones, creating a comprehensive journey through microservices architecture.

---

## 📂 File Organization Tips

- Keep each practical in its own directory
- Include screenshots in `screenshots/` subdirectories
- Maintain a `readme.md` documenting your implementation
- Use `.gitignore` to exclude build artifacts and dependencies
- Make regular commits with descriptive messages

---

## 🎓 Acknowledgments

This module is based on industry best practices and current microservices standards. All practicals are designed to provide hands-on experience with real-world technologies and patterns used in production environments.

---


---

## 📄 License

This repository and all practical work are part of academic coursework for WEB303.

---

