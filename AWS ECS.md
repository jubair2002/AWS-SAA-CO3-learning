# AWS ECS - Elastic Container Service

Last modified: 24 May 2026

## What is AWS ECS?

Amazon Elastic Container Service (ECS) is **AWS's container orchestration service** that allows you to run, manage, and scale Docker containers.
ECS handles the infrastructure management, scheduling, and orchestration of containerized applications.

- Run Docker containers at scale on AWS infrastructure
- Automatic container placement and scheduling
- Integration with other AWS services (ALB, RDS, CloudWatch, etc.)
- No need to manage the container orchestration engine
- Cost-effective compared to self-managed Kubernetes

---

## Why do we need ECS?

ECS addresses the challenge of managing containers across a fleet of servers without manual orchestration.

Common use cases:
- **Microservices architecture**: Run multiple containerized services
- **Batch processing**: Schedule and run batch jobs in containers
- **Web applications**: Deploy scalable web applications
- **CI/CD pipelines**: Run build and deployment tasks
- **Data processing**: Process data at scale using containers

Benefits:
- Simplified container management
- Automatic scaling based on demand
- Highly available across multiple AZs
- Integration with AWS security, monitoring, and load balancing services
- Pay only for compute resources used

---

## Key Components of ECS

### 1. Task

A **task** is the smallest unit of deployment in ECS.

- Instantiation of a task definition
- Represents running container(s)
- Contains one or more containers
- Has resource allocation (CPU, memory)
- Can be transient (run once) or long-running

### 2. Task Definition

A **task definition** is a blueprint for launching tasks, similar to a Docker Compose file.

- Specifies Docker image to use
- Container port mappings
- CPU and memory allocation
- Environment variables
- Log configuration
- Volume mounts
- Container dependencies

Example task definition structure:
```
- Family name: myapp-task
- Container name: myapp-container
- Image: myapp:latest
- Memory: 512 MB
- CPU: 256
- Port mappings: 8080:8080
- Logging: CloudWatch logs
```

### 3. Service

A **service** ensures desired number of tasks are running at all times.

- Maintains desired count of tasks
- Automatically restarts failed tasks
- Integrates with load balancers for traffic distribution
- Handles rolling deployments and updates
- Scales based on metrics

### 4. Cluster

A **cluster** is a logical grouping of infrastructure where tasks run.

- Collection of EC2 instances (EC2 launch type)
- Serverless infrastructure (Fargate launch type)
- Containers on cluster instances are managed by ECS Agent
- Resources are shared among services running on cluster

---

## Launch Types

ECS supports two launch types:

### 1. EC2 Launch Type

You manage EC2 instances, ECS manages containers.

**Characteristics:**
- You launch and manage EC2 instances
- ECS Agent runs on each EC2 instance
- Containers are scheduled to run on instances
- Pay for EC2 instances regardless of container load
- More control over infrastructure
- Better for long-running services

**When to use:**
- Need specific hardware requirements
- Running GPU-intensive workloads
- Want to optimize for cost with reserved instances
- Need specific OS or kernel modules

### 2. Fargate Launch Type

AWS manages both infrastructure and containers (serverless).

**Characteristics:**
- No EC2 instances to manage
- AWS manages the underlying infrastructure
- Pay only for container resources used
- Simpler to operate
- Automatic scaling
- Better for variable workloads

**When to use:**
- Want serverless container deployment
- Have variable workloads (batch jobs, intermittent tasks)
- Prefer operational simplicity
- Multiple small services running intermittently

---

## ECS Cluster Modes

### 1. Capacity Providers (Recommended)

Automatically manage capacity (EC2 + Fargate balance).

- Define desired capacity in terms of vCPU/memory
- ECS automatically selects EC2 or Fargate for optimal cost
- Auto-scaling based on demand
- Better resource utilization

### 2. Traditional EC2 Cluster

Manual instance management.

- Add/remove EC2 instances manually
- Less automated
- More control but more operational overhead

---

## Load Balancing with ECS

ECS services can integrate with load balancers for traffic distribution.

### Application Load Balancer (ALB)

Best for HTTP/HTTPS traffic:
- Host-based routing (example.com vs api.example.com)
- Path-based routing (/api/* vs /static/*)
- Layer 7 (application layer) routing
- Perfect for microservices

### Network Load Balancer (NLB)

Best for high performance and non-HTTP protocols:
- Extreme performance requirements
- UDP, raw TCP protocols
- Millions of requests per second
- Ultra-low latency

### Classic Load Balancer (ELB)

Legacy option (not recommended):
- Basic load balancing
- Suitable only for simple use cases

---

## Auto Scaling in ECS

ECS services can automatically scale based on metrics.

### Service Auto Scaling

Adjust number of running tasks based on:
- CPU utilization
- Memory utilization
- Custom CloudWatch metrics
- Scaling policies (target tracking, step scaling)

### Cluster Auto Scaling

For EC2 launch type: add/remove EC2 instances as needed.

---

## ECS vs EKS vs Docker Swarm

| Feature | ECS | EKS | Docker Swarm |
|---------|-----|-----|-------------|
| **Orchestration** | AWS managed | AWS managed Kubernetes | Docker native |
| **Complexity** | Simple | Complex | Simple |
| **Learning curve** | Easy | Steep | Easy |
| **Ecosystem** | AWS services | Kubernetes ecosystem | Docker ecosystem |
| **Use case** | AWS-centric | Multi-cloud, standard K8s | Small deployments |
| **Cost** | Moderate | Higher (K8s infrastructure) | Lower |
| **Scaling** | Excellent | Excellent | Good |

---

## ECS Architecture Best Practices

### 1. Task Definition Versioning

- Create new task definition versions for updates
- Allows rollback if needed
- Keep previous versions for troubleshooting

### 2. Resource Allocation

- Define CPU and memory limits for each container
- Prevent resource starvation
- Monitor actual vs allocated resources

### 3. Logging

Configure centralized logging:
- CloudWatch Logs
- Splunk
- Datadog
- Container Insights for monitoring

### 4. Health Checks

Define container health checks:
- Endpoint that indicates container is healthy
- ECS automatically restarts unhealthy containers
- Improve service reliability

### 5. Environment Variables and Secrets

- Use environment variables for non-sensitive config
- Use AWS Secrets Manager for sensitive data (passwords, API keys)
- Reference secrets in task definition

### 6. Networking

- Tasks in VPC for network isolation
- Security groups for traffic control
- Service discovery for inter-task communication

---

## ECS Service Deployment Strategies

### 1. Rolling Deployment

Gradually replace old tasks with new ones.

**Characteristics:**
- Zero downtime
- Maintains service availability
- Can take time for large deployments
- Risk of inconsistency (old and new versions running)

### 2. Blue/Green Deployment

Switch traffic between two identical environments.

**Characteristics:**
- Instant cutover
- Easy rollback
- More resource intensive (two environments)
- Less risk of version mismatch

### 3. Canary Deployment

Gradually route traffic to new version.

**Characteristics:**
- Risk mitigation for new deployments
- Monitor metrics before full rollout
- Requires more sophisticated routing

---

## Key Integrations

### CloudWatch

- Monitor container logs
- Track metrics (CPU, memory, network)
- Set alarms and triggers

### ALB/NLB

- Distribute traffic to tasks
- Health checks
- Path-based routing

### RDS

- Connect containers to relational databases
- Connection pooling recommended

### S3

- Store container images in ECR
- Access S3 from containers for data

### Secrets Manager

- Securely pass secrets to containers
- Automatic credential rotation

### IAM

- Control task permissions
- Service roles for task execution

---

## Cost Optimization for ECS

1. **Use Fargate Spot**: Run non-critical tasks on Fargate Spot for 70% savings
2. **Optimize resource allocation**: Monitor actual usage and adjust limits
3. **Use Reserved Capacity**: For predictable workloads on EC2 launch type
4. **Right-size containers**: Match resources to application needs
5. **Consolidate services**: Run multiple small services on same infrastructure
6. **Monitor unused resources**: Identify and remove idle services

---

## Common Use Cases

1. **Containerized Web Applications**: Run scalable web apps
2. **Microservices**: Deploy multiple services independently
3. **Batch Processing**: Run scheduled or triggered batch jobs
4. **CI/CD Pipeline Tasks**: Build and test containers
5. **Data Processing**: ETL jobs using containers
6. **API Services**: High-performance REST APIs
7. **Background Workers**: Async job processing

---

## Getting Started with ECS

1. Create or push Docker image to ECR (Elastic Container Registry)
2. Define task definition with container specifications
3. Create ECS cluster (EC2 or Fargate)
4. Create service with desired task count
5. Configure load balancer for external traffic
6. Set up CloudWatch monitoring and alarms
7. Configure auto-scaling policies
8. Monitor logs and metrics
