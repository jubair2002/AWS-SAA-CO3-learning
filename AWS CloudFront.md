# AWS CloudFront - Content Delivery Network

Last modified: 24 May 2026

## What is CloudFront?

CloudFront is AWS's **content delivery network (CDN)** service that accelerates delivery of your web content globally.
It caches content at edge locations around the world, reducing latency and improving user experience.

- Distributes content from origin servers to edge locations worldwide
- Caches static and dynamic content for faster delivery
- Reduces bandwidth usage and server load
- Provides DDoS protection and security features
- Supports multiple origin types (S3, EC2, ALB, custom origins)

---

## Why do we need CloudFront?

CloudFront solves the challenge of delivering content quickly to users across the globe.

Common use cases:
- **Website acceleration**: Faster website loading for global users
- **API acceleration**: Reduced latency for API calls
- **Streaming**: Distributing video or media content
- **Software distribution**: Faster downloads of applications or patches
- **Security**: DDoS protection and WAF integration

Benefits:
- Significantly reduced latency for end users
- Lower bandwidth costs
- Improved availability through edge caching
- Easy integration with other AWS services
- Built-in security features

---

## How CloudFront Works

1. User requests content from your website/application
2. Request is routed to the nearest CloudFront edge location
3. If content is cached at the edge, it's delivered immediately
4. If not cached, CloudFront fetches from the origin server
5. Content is cached at the edge for future requests
6. Response is delivered back to the user
7. Subsequent requests from nearby users get cached content

The edge location acts as an intermediary between users and your origin server.

> Important: CloudFront is **global** and automatically routes requests to the nearest edge location.  
> This provides automatic failover and improved performance.

---

## Key Concepts

### Origin

The origin is the source of content that CloudFront will distribute.

- **S3 Bucket**: Static website files, images, documents
- **EC2 Instance**: Dynamic web server
- **Application Load Balancer (ALB)**: Load-balanced application servers
- **Custom Origin**: Any web server (on-premises, third-party)

Origin is configured with:
- Domain name or IP address
- Protocol (HTTP/HTTPS)
- Port number
- Path (optional)

### Edge Locations and Regional Caches

CloudFront has a global network of edge locations:

- **Edge Locations**: First point of contact for users; cache content close to end users
- **Regional Caches**: Intermediate cache layer between edge locations and origin
- Over 500+ edge locations globally
- Reduces requests reaching the origin server

### Distribution

A distribution is a CloudFront configuration that specifies:
- Which origin to fetch content from
- Caching behavior
- Access restrictions
- Security settings

Types of distributions:
- **Web Distribution**: For websites, APIs, web applications
- **RTMP Distribution**: For streaming media (legacy)

---

## CloudFront Features

### 1. Caching

CloudFront caches content based on:
- Cache-Control headers from origin
- TTL (Time To Live) settings
- Query strings and cookies
- Request headers

Caching behavior can be customized per path pattern (e.g., `/api/*` vs `/static/*`).

### 2. Origin Shield

An additional caching layer between regional caches and origin servers.

- Further reduces origin load
- Improves cache hit ratio
- Particularly useful for frequently accessed content
- Adds minimal latency

### 3. Security Features

CloudFront provides multiple security mechanisms:

- **HTTPS/SSL/TLS**: Encrypt data in transit
- **Field-Level Encryption**: Encrypt sensitive fields in POST requests
- **Web Application Firewall (WAF)**: Protect against common web exploits
- **DDoS Protection**: AWS Shield Standard (included) and Shield Advanced
- **Signed URLs and Signed Cookies**: Restrict content access to authorized users
- **Origin Access Identity (OAI)**: Allow CloudFront to access private S3 buckets

### 4. Geo-Restriction

Control content availability by geography:

- **Whitelist**: Allow content only from specific countries
- **Blacklist**: Block content from specific countries
- Uses GeoIP database for country determination

### 5. Lambda@Edge

Run serverless functions at edge locations:

- Process requests/responses at CloudFront edge locations
- Customize content delivery without modifying origin
- Use cases: Request validation, response modification, access control
- Minimal latency since code runs at the edge

### 6. Query String and Header Handling

Control how CloudFront caches variants of the same URL:

- Forward specific headers to origin
- Include query strings in cache key
- Cookie handling for personalized content

---

## CloudFront vs Other AWS Services

### CloudFront vs S3 Transfer Acceleration

| Feature | CloudFront | S3 Transfer Acceleration |
|---------|-----------|-------------------------|
| Use case | General content delivery | Uploading large files to S3 |
| Caching | Yes | No caching |
| Origin types | Multiple | S3 only |
| Global network | Edge locations + regional caches | Edge locations only |

### CloudFront vs ALB/ELB

- **CloudFront**: CDN for content delivery across globe
- **ALB/ELB**: Load balancing within AWS or on-premises
- Can be used together: CloudFront in front of ALB

---

## CloudFront Pricing

CloudFront pricing is based on:

1. **Data Transfer Out** (primary cost): Per GB delivered to users by region
2. **HTTP/HTTPS Requests**: Per number of requests
3. **Invalidation Requests**: Per invalidation request (first 3,000/month free)
4. **Lambda@Edge**: Per execution and data transfer
5. **Origin Shield**: Optional additional fee per request

> Tip: CloudFront often reduces total costs by decreasing origin server load and bandwidth.

---

## Best Practices

1. **Use S3 with CloudFront**: For static content, use S3 with CloudFront instead of serving directly from EC2
2. **Set appropriate TTL**: Balance between freshness and cache hit ratio
3. **Enable compression**: Reduce data transfer with gzip/brotli compression
4. **Use wildcard SSL certificates**: For multiple subdomains
5. **Monitor cache hit ratio**: Aim for high cache hit ratios (typically 80%+)
6. **Enable logging**: Log CloudFront requests to S3 for analysis
7. **Use multiple origins**: For redundancy and failover
8. **Implement security**: Use WAF, signed URLs, and Origin Access Identity
9. **Invalidate strategically**: Invalidate only changed content to avoid costs
10. **Test thoroughly**: Verify content delivery before going live

---

## CloudFront with S3

### Secure S3 Access via CloudFront

Use **Origin Access Identity (OAI)** to:
- Allow CloudFront to access private S3 bucket
- Block direct public access to S3
- Ensure all traffic goes through CloudFront

Steps:
1. Create S3 bucket (not publicly accessible)
2. Create CloudFront distribution with S3 as origin
3. Create Origin Access Identity
4. Update S3 bucket policy to allow CloudFront OAI
5. Users access content through CloudFront URL, not S3 URL

This ensures:
- Content can only be accessed through CloudFront
- Direct S3 URL access is blocked
- CloudFront caching benefits are utilized
- Cost control through caching

---

## CloudFront Invalidation

When you need to update content before cache expires:

- Create invalidation to remove cached objects
- Use path patterns: `/index.html`, `/images/*`, `/*` (all files)
- First 3,000 invalidations/month are free
- Additional invalidations cost per path

> Tip: Avoid invalidating everything. Target specific paths to minimize costs.

---

## Common Use Cases

1. **Static Website Hosting**: Serve website from S3 behind CloudFront
2. **API Acceleration**: Cache API responses at edge locations
3. **Dynamic Content**: Combine caching for static assets with origin for dynamic content
4. **Media Streaming**: Distribute video and audio content efficiently
5. **Software Distribution**: Fast download delivery for applications
6. **A/B Testing**: Use Lambda@Edge to serve different content variants
7. **Mobile App Delivery**: Reduce bandwidth and improve load times for mobile apps
