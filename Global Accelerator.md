# AWS Global Accelerator

Last modified: 31 May 2026

## What Is AWS Global Accelerator

AWS Global Accelerator is a networking service that improves availability and performance of your global applications by routing user traffic through AWS's optimized global network instead of the public internet.

## Why We Need Global Accelerator

- **Reduced latency**: Routes traffic via AWS's private, optimized network backbone instead of the public internet.
- **Improved availability**: Automatic failover to healthy endpoints across regions.
- **Static IP addresses**: Provides two static anycast IPs that remain constant even when you update endpoints.
- **DDoS protection**: Built-in protection via AWS Shield Standard; can upgrade to Shield Advanced.
- **Packet loss reduction**: AWS's optimized network path reduces packet loss on lossy internet links.
- **Consistent performance**: Users experience the same performance regardless of geographic location.

## Challenges Without Global Accelerator

- **High latency for global users**: Traffic traverses the public internet with unpredictable paths and delays.
- **Packet loss on poor connections**: Internet path quality varies; mobile and international users suffer.
- **Manual failover or complex DNS**: Route 53 failover is DNS-based; requires TTL to propagate changes.
- **IP changes required**: Updating backend resources requires updating endpoints, potentially changing user-visible IPs.
- **Limited control over routing path**: Cannot optimize the network path users' traffic takes.
- **DDoS risk**: Public internet paths expose applications to DDoS without integrated protection.

## How Global Accelerator Solves These Challenges

- **AWS backbone routing**: Uses AWS's optimized private network to reduce hops and latency.
- **Anycast IP addresses**: Two static IPs that always remain the same, even when endpoints change.
- **Instant failover**: Detects unhealthy endpoints and switches traffic immediately (no DNS TTL delay).
- **Multi-region support**: Seamlessly route traffic across AWS regions and on-premises resources.
- **Traffic dials and endpoint weights**: Fine-grained control over traffic distribution.
- **Shield integration**: Built-in DDoS protection with optional Shield Advanced.

## Global Accelerator Components

### 1) Accelerator
- Top-level resource that provides static anycast IPs.
- Associated with 1-2 static public IP addresses.
- Can be used worldwide or in specific regions.

### 2) Listener
- Processes incoming traffic on specific ports and protocols (TCP/UDP).
- Routes traffic to appropriate endpoint groups based on rules.

### 3) Endpoint Groups
- Collection of endpoints in a specific region or traffic dial setting.
- Each endpoint group has health checks and traffic allocation.
- Can be in different AWS regions or on-premises.

### 4) Endpoints
- Individual resources that receive traffic: ALB, NLB, EC2, Elastic IP, S3.
- Health status determines if they receive traffic.

## Global Accelerator vs Route 53

| Feature | Global Accelerator | Route 53 |
|---------|-------------------|----------|
| **Routing** | Network layer (AWS backbone) | DNS layer (public internet) |
| **IP Address** | Static anycast IPs | Resolves to endpoint IPs (can change) |
| **Failover** | Instant (~30 seconds) | DNS-based (depends on TTL) |
| **Latency** | Optimized AWS path | Public internet path |
| **Use Case** | Non-HTTP protocols, extreme performance | DNS management, HTTP/HTTPS services |
| **Cost** | Hourly charge + data transfer | DNS query charges |

## Quick Decision: Global Accelerator or Route 53?

- **Use Global Accelerator**: Non-HTTP protocols (gaming, IoT, real-time), extreme latency sensitivity, need static IPs.
- **Use Route 53**: HTTP/HTTPS services, simple routing policies, cost-sensitive applications.
- **Use Both**: Global Accelerator for network routing + Route 53 for DNS aliases to Global Accelerator IPs.

## Practical: Global Accelerator Multi-Region Setup Lab

### Goal

Create a Global Accelerator that routes traffic across two regions to ALBs, with automatic failover and health checks.

### Architecture Summary

- Global Accelerator with two static anycast IPs.
- Listener on port 80 (TCP).
- Two endpoint groups: one in us-east-1, one in us-west-2.
- ALBs in each region behind endpoint groups.
- Health checks monitoring each ALB.
- Traffic dials to control traffic allocation to regions.

### Prerequisites

- Two ALBs deployed in different regions (us-east-1 and us-west-2).
- Both ALBs registered with healthy backend instances.
- Security groups allowing traffic on port 80/443 from Global Accelerator.

### Step 1: Verify ALBs Are Running in Both Regions

Confirm ALBs in both regions are active and serving traffic.

### Step 2: Create Global Accelerator

- Go to EC2 Console -> Global Accelerator -> Create accelerator.
- Name: `my-global-app` (or descriptive name).
- IP address type: `IPv4`.
- Enabled: `Yes`.

### Step 3: Note Static IPs

Observe the two static anycast IPs provided by Global Accelerator.

These IPs remain constant even if you change endpoints. Use these IPs in your application or DNS records.

### Step 4: Create Listener

- Select the accelerator -> Listeners -> Add listener.
- Protocol: `TCP`.
- Port: `80` (or 443 for HTTPS).
- Client affinity: `None` (or `Source IP` for sticky sessions).

### Step 5: Add Endpoint Group - US-East Region

- Listener -> Endpoint groups -> Add endpoint group.
- Region: `US East (N. Virginia)`.
- Traffic dial: `100` (100% of traffic to this group).
- Endpoint type: `Application Load Balancer`.
- Endpoint: Select the ALB in us-east-1.
- Client port: `80`.
- Health check protocol: `HTTP`.
- Health check path: `/` (or `/health` if available).
- Health check interval: `30 seconds`.
- Threshold count: `3` (3 consecutive failures mark unhealthy).

### Step 6: Add Endpoint Group - US-West Region

- Create another endpoint group with same configuration but for us-west-2 ALB.
- Region: `US West (Oregon)`.
- Traffic dial: `100`.
- Endpoint: Select the ALB in us-west-2.

### Step 7: Test Global Accelerator from Different Regions

Use curl with Global Accelerator static IP to send traffic:

```bash
curl http://1.2.3.4  # Replace with actual static IP
```

Send requests from US-East location and verify low latency response.

Send requests from US-West location and verify traffic routes to closer region.

### Step 8: Monitor Health Status

Go to Global Accelerator console -> Endpoint groups and verify both endpoint groups show green (healthy).

### Step 9: Simulate Failure in One Region

Stop the ALB or instances in one region (e.g., us-east-1).

### Step 10: Verify Automatic Failover

- Health check for us-east-1 endpoint group transitions to unhealthy.
- All traffic now routes to us-west-2 endpoint group.
- Requests to Global Accelerator static IPs respond with us-west-2 instance.

### Step 11: Use Traffic Dials for Gradual Rollout (Optional)

If both regions are healthy and you want 70% traffic to us-east, 30% to us-west:

- Edit us-east-1 endpoint group: Traffic dial = `70`.
- Edit us-west-2 endpoint group: Traffic dial = `30`.

Traffic automatically distributes based on dial values.

### Step 12: Restore Failed Region

Restart the stopped ALB/instances in us-east-1.

Health check recovers and traffic automatically rebalances.

## Verification Checklist

- Two static anycast IPs are assigned to the accelerator.
- Listener is created and active on port 80.
- Both endpoint groups show healthy status.
- Curl to Global Accelerator static IP receives responses.
- Requests from different regions show low latency.
- When one endpoint group fails, traffic routes to the healthy one.
- When failed region recovers, traffic rebalances automatically.

## Basic Troubleshooting

- **Endpoints showing unhealthy**: Check ALB is running, security groups allow traffic from Global Accelerator source, and health check path is correct.
- **No response from Global Accelerator IP**: Verify listener is enabled, endpoint groups are healthy, and traffic dials are > 0.
- **High latency still observed**: Global Accelerator routes via AWS backbone; public internet path still taken at origin. Ensure ALBs are well-connected.
- **Traffic not balanced**: Verify both endpoint groups have traffic dial > 0 and are both healthy.

## Advanced: Combining Global Accelerator and Route 53

For maximum flexibility, use Global Accelerator with Route 53:

1. Create Global Accelerator with static IPs.
2. In Route 53, create an `A` record for your domain.
3. Point Route 53 `A` record to Global Accelerator static IPs.
4. Users query Route 53 (returns Global Accelerator IPs) and then connect to static IPs (routed via AWS backbone).

This gives you:
- DNS management via Route 53.
- Optimized network routing via Global Accelerator.
- Flexibility to change endpoints without changing IPs.
