# Route 53

Last modified: 31 May 2026

## What Is Route 53

Route 53 is AWS's managed Domain Name System (DNS) and domain registration service. It translates human-readable domain names (like example.com) into IP addresses that computers use to route traffic to the correct resources.

## Why We Need Route 53

- **Global DNS service**: Route traffic to resources across multiple regions and continents.
- **Domain registration**: Buy and manage domain names directly from AWS.
- **Health checks**: Route traffic only to healthy endpoints, removing failed ones automatically.
- **Traffic management**: Distribute traffic based on geography, latency, weighted policies, and failover rules.
- **Single control plane**: Manage DNS and routing policies from one AWS service.
- **High availability**: Anycast routing ensures your DNS is always reachable.

## Challenges Without Route 53

- **Manual DNS management**: Operating your own DNS infrastructure requires expertise and maintenance.
- **No intelligent routing**: Basic DNS cannot route based on latency, health, or geographic location.
- **Single point of failure**: Self-managed DNS servers can go down, causing complete service unavailability.
- **Scaling complexity**: Handling global traffic spikes requires distributed DNS infrastructure.
- **Domain management fragmentation**: Separate providers for domains and DNS routing adds complexity.
- **No health-aware failover**: Cannot automatically remove unhealthy endpoints from DNS responses.

## How Route 53 Solves These Challenges

- **Managed DNS**: Fully managed service eliminates infrastructure maintenance burden.
- **Intelligent routing policies**: Route based on latency, geography, weighted distribution, and health status.
- **Health checks**: Continuous monitoring of endpoint health with automatic failover.
- **Global distributed network**: Uses anycast routing to serve DNS queries from the nearest location.
- **Integrated with AWS**: Direct integration with ALB, NLB, CloudFront, S3, and other AWS services.
- **Traffic flow**: Visual traffic policy editor for complex routing scenarios.

## Route 53 Routing Policies

### 1) Simple Routing
- Routes to a single resource (e.g., one EC2 instance or ALB).
- No intelligent routing logic.
- **Use when**: Single target with no failover needs.

### 2) Weighted Routing
- Distributes traffic across multiple resources based on assigned weights.
- Example: 70% to new version, 30% to old version (canary deployment).
- **Use when**: A/B testing, canary releases, or gradual traffic migration.

### 3) Latency-Based Routing
- Routes to the resource in the region with lowest latency for the user.
- Automatically selects the closest endpoint.
- **Use when**: Multi-region deployments needing optimal user performance.

### 4) Failover Routing
- Primary resource receives all traffic; if unhealthy, traffic switches to secondary.
- Requires health checks on primary resource.
- **Use when**: Active-passive high availability setup.

### 5) Geolocation Routing
- Routes based on the geographic location of the requester.
- Can serve different content by region/country.
- **Use when**: Content localization, legal compliance, or region-specific services.

### 6) Geoproximity Routing
- Routes based on both resource location and user location.
- Allows bias adjustment to prefer certain regions.
- **Use when**: Fine-grained geographic control without strict country boundaries.

### 7) Multi-Value Answer Routing
- Returns multiple healthy IP addresses in random order.
- Client chooses which to use (simple client-side load balancing).
- **Use when**: Simple load distribution without a centralized load balancer.

## Quick Decision: Which Routing Policy When?

- **Simple**: Single target, no special routing logic.
- **Weighted**: Canary deployments, A/B testing, traffic splitting.
- **Latency-based**: Multi-region for performance optimization.
- **Failover**: Active-passive HA with health checks.
- **Geolocation**: Content localization and regional compliance.
- **Geoproximity**: Geographic control with flexibility.
- **Multi-value**: Simple client-side load balancing.

## Route 53 Health Checks

Health checks monitor endpoints and automatically remove unhealthy ones from DNS responses.

### Health Check Types

- **Endpoint health checks**: Monitor HTTP/HTTPS/TCP endpoints.
- **Calculated health checks**: Combine results from multiple health checks.
- **CloudWatch alarm health checks**: Use CloudWatch alarms as health check indicators.

### Health Check Configuration

- **Protocol**: HTTP, HTTPS, or TCP.
- **Port**: Custom port if needed.
- **Path**: For HTTP/HTTPS checks.
- **Interval**: Standard (30s) or fast (10s).
- **Failure threshold**: Number of failures before marking unhealthy.

## Practical: Failover Routing with Health Checks Lab

### Goal

Create a failover DNS setup where Route 53 automatically switches traffic to a secondary resource when the primary becomes unhealthy.

### Architecture Summary

- Primary EC2 instance (or ALB) in Region A serving traffic.
- Secondary EC2 instance (or ALB) in Region B as standby.
- Route 53 health check monitoring primary resource.
- Failover routing policy on Route 53 record.
- Automatic DNS failover when primary health check fails.

### Prerequisites

- Domain registered in Route 53 (or external domain with NS records pointing to Route 53).
- Two EC2 instances (or ALBs) in different regions with identical application.
- Security groups allowing HTTP/HTTPS from Route 53 health checkers.
- Primary instance running and serving traffic.

### Step 1: Verify Both Instances Are Running

Confirm both primary and secondary instances are up and serving traffic.

### Step 2: Create Health Check for Primary Resource

- Go to Route 53 Console -> Health Checks -> Create health check.
- Type: `Endpoint`.
- Protocol: `HTTP` or `HTTPS`.
- IP address/hostname: Primary instance public IP or ALB DNS name.
- Port: `80` (or 443 for HTTPS).
- Path: `/` (or application health endpoint).
- Interval: `30 seconds`.
- Failure threshold: `3` (3 consecutive failures mark as unhealthy).

### Step 3: Create Failover DNS Records

- Go to Route 53 Console -> Hosted zones -> Your domain -> Create records.
- **Primary Record**:
  - Name: `example.com` (or subdomain).
  - Type: `A`.
  - Value: Primary instance public IP or ALB DNS name.
  - Routing policy: `Failover`.
  - Failover record type: `Primary`.
  - Health check: Select the created health check.
  - TTL: `60` seconds.

- **Secondary Record**:
  - Same name and type as primary.
  - Value: Secondary instance public IP or ALB DNS name.
  - Routing policy: `Failover`.
  - Failover record type: `Secondary`.
  - TTL: `60` seconds.

### Step 4: Test Primary Instance Serving Traffic

Use nslookup or curl to resolve the domain and verify primary instance responds.

```bash
nslookup example.com
curl http://example.com
```

Expected output should show primary instance IP and response.

### Step 5: Simulate Primary Failure

Stop the primary instance or stop the application to trigger health check failures.

```bash
# On primary instance, stop the web server
sudo systemctl stop apache2  # or nginx
```

### Step 6: Monitor Health Check Status

Go to Route 53 Console -> Health Checks and observe the primary health check transition to unhealthy.

### Step 7: Verify DNS Failover to Secondary

After health check failure (wait for TTL + failure threshold time), resolve the domain again.

```bash
nslookup example.com
curl http://example.com
```

Expected output should now show secondary instance IP and response.

### Step 8: Restore Primary and Verify Failback

Restart the primary instance and application.

```bash
sudo systemctl start apache2  # or nginx
```

After health check recovers, DNS should switch back to primary.

## Verification Checklist

- Health check status shows healthy for primary when instance is running.
- nslookup resolves domain to primary instance IP.
- curl/browser accesses primary instance successfully.
- After stopping primary, health check transitions to unhealthy.
- After failover, nslookup resolves to secondary instance IP.
- Secondary instance responds to requests.
- After restoring primary, failover reverses automatically.

## Basic Troubleshooting

- **Health check always unhealthy**: Verify instance is reachable from Route 53 health checkers (check security groups, network ACLs, and route tables).
- **DNS still pointing to unhealthy primary**: Check health check configuration is correct and wait for TTL to expire.
- **Failover not happening**: Ensure secondary record is set to `Secondary` failover type and has no health check attached.
- **Domain not resolving**: Verify NS records in domain registrar point to Route 53 nameservers, or domain is registered in Route 53.
