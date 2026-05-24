# AWS EKS - Elastic Kubernetes Service

Last modified: 24 May 2026

## What is AWS EKS?

Amazon Elastic Kubernetes Service (EKS) is **AWS's managed Kubernetes service**.
It provides a managed control plane for Kubernetes, eliminating the need to install and operate your own Kubernetes infrastructure.

- Fully managed Kubernetes control plane
- AWS handles master node operations and patches
- Deploy on EC2 instances or Fargate (serverless)
- Integrated with AWS services (IAM, VPC, CloudWatch, etc.)
- Multi-AZ deployment for high availability
- Compatible with standard Kubernetes tools and applications

---

## Why do we need EKS?

EKS addresses the operational complexity of managing Kubernetes clusters while providing deep AWS integration.

Common use cases:
- **Complex microservices**: Manage multiple containerized services
- **Multi-region deployments**: Deploy Kubernetes across AWS regions
- **Hybrid workloads**: Run on AWS and on-premises with consistent platform
- **Enterprise applications**: Need mature container orchestration
- **Auto-scaling applications**: Automatic scaling based on demand

Benefits:
- Eliminates Kubernetes control plane management overhead
- AWS handles availability, security patches, and updates
- Deep integration with AWS services
- Multi-AZ deployment for high availability
- IAM-based access control
- Cost-effective compared to self-managed Kubernetes

---

## Key Differences: ECS vs EKS

| Feature | ECS | EKS |
|---------|-----|-----|
| **Orchestration** | AWS proprietary | Standard Kubernetes |
| **Learning curve** | Easy | Steep |
| **Ecosystem** | AWS-focused | Kubernetes ecosystem (helm, operators, etc.) |
| **Multi-cloud** | AWS only | Works on AWS, GCP, Azure, on-premises |
| **Standardization** | AWS specific | Industry standard |
| **Operational complexity** | Lower | Higher |
| **Control plane** | Simple | More control and flexibility |
| **Community** | AWS community | Large Kubernetes community |

**Choose ECS if:** AWS-centric, simplicity is priority  
**Choose EKS if:** Multi-cloud, need Kubernetes features, standard tooling

---

## EKS Architecture

### Control Plane (AWS Managed)

The control plane runs on AWS-managed infrastructure:

- **API Server**: RESTful API for cluster management
- **Scheduler**: Places pods on worker nodes
- **Controller Manager**: Manages cluster state
- **etcd**: Cluster database
- **DNS**: Service discovery

AWS manages all of this automatically.

### Data Plane (Your Responsibility)

The data plane consists of worker nodes where containers run:

**Compute options:**
- **EC2 nodes**: Traditional managed instances
- **Fargate**: Serverless container compute
- **On-premises nodes**: Connect on-premises servers to cluster

---

## EKS Components

### 1. Cluster

A logical grouping of resources running Kubernetes.

- One control plane (AWS managed)
- One or more node groups (worker nodes)
- Spans multiple AZs for high availability
- Custom networking via VPC

### 2. Node Groups

Managed groups of EC2 instances (or Fargate).

**Auto Scaling Group (ASG):**
- Automatically adds/removes nodes based on demand
- Configured with desired capacity, min, and max nodes
- Integrates with Kubernetes scheduler

**Managed node groups:**
- AWS manages EC2 instance updates and patches
- Simplified node lifecycle management

### 3. Pods

Smallest deployable unit in Kubernetes.

- One or more containers (usually one)
- Shared networking namespace
- Ephemeral (temporary)

### 4. Deployments

Declarative specification for running pods.

- Defines desired number of replicas
- Automatic scaling and replacement
- Rolling updates

### 5. Services

Abstracts pod networking.

- **ClusterIP**: Internal service (default)
- **NodePort**: Exposes service on node port
- **LoadBalancer**: Creates AWS load balancer
- **ExternalName**: Maps to external DNS name

### 6. Namespaces

Virtual clusters within a cluster.

- Resource isolation
- RBAC boundaries
- Multi-tenant support

---

## EKS Networking

### VPC Integration

EKS clusters run within a VPC:

- Pods get IP addresses from VPC CIDR
- Security groups control traffic
- Network policies for pod-to-pod communication
- Direct AWS network integration

### Container Network Interface (CNI)

EKS uses AWS VPC CNI plugin:

- Each pod gets an IP from VPC
- Enhanced networking capabilities
- Security group per pod option
- Direct network performance

### Service Discovery

- Kubernetes DNS for service discovery
- CoreDNS as cluster DNS
- Service names resolvable within cluster

---

## Kubernetes Concepts

### Deployments and ReplicaSets

Manage pod replicas:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp
spec:
  replicas: 3
  template:
    spec:
      containers:
      - name: myapp
        image: myapp:latest
        resources:
          limits:
            cpu: 500m
            memory: 256Mi
```

### StatefulSets

Manage stateful applications:

- Persistent pod identities
- Ordered deployment and scaling
- Stable network identities
- Example: Databases, message queues

### DaemonSets

Run pod on every node:

- System logging
- Node monitoring
- Network plugins

### ConfigMaps and Secrets

Configuration management:

- **ConfigMaps**: Non-sensitive configuration
- **Secrets**: Sensitive data (passwords, API keys)

---

## Auto Scaling in EKS

### 1. Horizontal Pod Autoscaler (HPA)

Automatically scales number of pods based on metrics.

**Metrics:**
- CPU utilization
- Memory utilization
- Custom metrics from CloudWatch

**Configuration:**
```yaml
minReplicas: 2
maxReplicas: 10
targetCPUUtilizationPercentage: 70
```

### 2. Cluster Autoscaler

Automatically adds/removes nodes based on pod scheduling needs.

- Increases nodes when pods can't be scheduled
- Decreases nodes when underutilized
- Respects node group min/max limits

### 3. Vertical Pod Autoscaler (VPA)

Adjusts CPU and memory requests/limits based on usage.

- Recommends right-sizing for containers
- Automatic adjustment based on metrics

---

## Security in EKS

### 1. IAM Integration

- IAM roles for service accounts (IRSA)
- Pod-level IAM permissions
- Fine-grained access control

### 2. Network Security

- Security groups at pod level
- Network policies for pod-to-pod communication
- VPC isolation

### 3. RBAC (Role-Based Access Control)

Kubernetes-native access control:

- Define roles and bindings
- User/service account permissions
- Namespace isolation

### 4. Pod Security

- Pod Security Policies (deprecated) / Pod Security Standards
- Container image scanning
- Runtime security

### 5. Encryption

- Secrets encryption at rest in etcd
- TLS for communication between components
- Encryption for AWS integrations

---

## EKS Add-ons

AWS provides managed add-ons:

### CoreDNS

Cluster DNS for service discovery.

### kube-proxy

Network proxy for Kubernetes services.

### VPC CNI

Container networking for pod networking.

### AWS Load Balancer Controller

Integrates AWS load balancers with Kubernetes services.

### Monitoring and Logging

- CloudWatch Container Insights
- Prometheus integration
- Managed logging add-on

---

## Monitoring and Logging in EKS

### CloudWatch Container Insights

Built-in monitoring dashboard:

- Cluster health metrics
- Pod performance
- Node metrics
- Application logs

### Persistent Logging

Configure centrally:

- CloudWatch Logs
- Elasticsearch
- Splunk
- Datadog

### Distributed Tracing

AWS X-Ray integration:

- Trace requests across services
- Performance analysis
- Error diagnosis

---

## EKS Best Practices

### 1. Resource Management

- Set requests and limits for all containers
- Monitor resource usage
- Right-size node instance types

### 2. High Availability

- Multi-AZ node deployment
- Pod disruption budgets
- Rolling update strategies

### 3. Security

- Implement network policies
- Use Pod Security Standards
- Enable RBAC
- Regular security scanning

### 4. Cost Optimization

- Use Fargate for variable workloads
- Spot instances for non-critical workloads
- Reserved capacity for predictable workloads
- Right-sizing and monitoring

### 5. Observability

- Implement comprehensive logging
- Use distributed tracing
- Monitor metrics and set alerts
- Regular health checks

### 6. GitOps

- Infrastructure as code
- Version control for cluster configuration
- Tools: ArgoCD, Flux
- Automated deployment pipelines

---

## Kubernetes Ecosystem Tools

Popular tools for EKS:

- **Helm**: Package manager for Kubernetes
- **Prometheus**: Monitoring and alerting
- **Grafana**: Metrics visualization
- **Istio**: Service mesh for advanced traffic management
- **Linkerd**: Lightweight service mesh
- **Sealed Secrets**: Secret management
- **Cert-Manager**: Certificate automation
- **Karpenter**: Advanced node provisioning

---

## Cost Considerations

1. **Control plane cost**: Fixed cost per cluster (~$0.10/hour)
2. **Compute cost**: EC2, Fargate, Spot instances
3. **Data transfer cost**: Between nodes and AWS services
4. **Add-on cost**: Some paid add-ons available
5. **Optimization**: Use Fargate Spot, Spot instances, Reserved Capacity

> Tip: EKS has lower overhead than self-managed Kubernetes but higher than ECS.

---

## Common Use Cases

1. **Microservices Architecture**: Complex multi-service deployments
2. **Multi-Cloud Strategy**: Run on AWS, GCP, Azure with same Kubernetes
3. **Migration from On-Premises**: Hybrid cloud deployments
4. **Complex Application Requirements**: Advanced scheduling needs
5. **Standardized DevOps Workflows**: Kubernetes-based CI/CD
6. **Large-Scale Applications**: Enterprise-grade container orchestration
7. **FinOps Optimization**: Fine-grained cost tracking and optimization

---

## Getting Started with EKS

1. Create EKS cluster (via AWS Console, CLI, or Terraform)
2. Configure kubectl to access cluster
3. Create node groups (EC2 or Fargate)
4. Deploy applications using kubectl or Helm
5. Configure load balancers for external access
6. Set up monitoring and logging
7. Implement RBAC and network policies
8. Configure auto-scaling (HPA and Cluster Autoscaler)
