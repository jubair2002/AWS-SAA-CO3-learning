# AWS Secrets Manager

Last modified: 24 May 2026

## What is AWS Secrets Manager?

AWS Secrets Manager is a **secret management service** that helps you rotate, manage, and retrieve database credentials, API keys, and other secrets.
It eliminates the need to hardcode sensitive information in application code or configuration files.

- Securely store secrets (passwords, API keys, database credentials)
- Automatic rotation of secrets
- Fine-grained access control with IAM
- Audit trail of secret access
- Encryption at rest and in transit
- Integration with AWS services and third-party applications

---

## Why do we need Secrets Manager?

Secrets Manager addresses critical security challenges around managing sensitive data.

Common use cases:
- **Database credentials**: Rotate RDS, Aurora passwords automatically
- **API keys**: Manage third-party service API keys
- **OAuth tokens**: Store and rotate OAuth refresh tokens
- **SSH keys**: Store and manage SSH key pairs
- **Application secrets**: Configuration secrets for applications
- **Service-to-service authentication**: Credentials between AWS services

Benefits:
- **Security**: Encrypt secrets and limit access with IAM
- **Compliance**: Audit trail and secret rotation
- **Automation**: Automatic secret rotation reduces manual management
- **Scalability**: Manage hundreds or thousands of secrets
- **Simplicity**: Centralized secret management
- **Cost**: Less than managing secrets infrastructure

---

## Key Concepts

### Secret

A **secret** is a piece of sensitive data stored in Secrets Manager.

**Types of secrets:**
- Database credentials (username, password, connection string)
- API keys and tokens
- OAuth tokens
- SSH keys
- Custom secrets (JSON objects)

Each secret has:
- **Name**: Unique identifier
- **Value**: The actual secret data
- **Versions**: Multiple versions for rotation
- **Metadata**: Tags, description, ARN
- **Rotation**: Schedule for automatic rotation

### Rotation

**Automatic secret rotation** updates secrets on a schedule.

- Reduces risk if credentials are compromised
- Configurable rotation schedule
- Lambda function handles rotation logic
- Default rotations for RDS databases

---

## Secret Versions

Secrets Manager maintains multiple versions of secrets:

**Version states:**
- **AWSCURRENT**: Currently active secret (used by applications)
- **AWSPENDING**: Staged for rotation (being tested)
- **AWSPREVIOUS**: Previous version (kept for compatibility)

**Rotation process:**
1. New version created (AWSPENDING)
2. Lambda rotation function updates credentials in target system
3. Lambda tests connection with new credentials
4. New version promoted to AWSCURRENT
5. Old version marked AWSPREVIOUS

This ensures no downtime during rotation.

---

## Accessing Secrets

### From Applications

Retrieve secrets via AWS SDK:

**Python Example:**
```python
import boto3

client = boto3.client('secretsmanager', region_name='us-east-1')

response = client.get_secret_value(SecretId='prod/db/password')
secret = response['SecretString']
```

**CLI Example:**
```bash
aws secretsmanager get-secret-value --secret-id prod/db/password
```

### Caching

For performance:
- Cache retrieved secrets in application memory
- Reduce API calls to Secrets Manager
- Configure appropriate TTL based on rotation schedule

---

## Built-in Rotation Functions

AWS provides pre-built rotation functions for common scenarios:

### RDS Databases

Automatic rotation for:
- MySQL
- PostgreSQL
- Oracle
- SQL Server

Rotation function:
1. Generates new password
2. Updates database user password
3. Tests connection
4. Stores new password in Secrets Manager

### RedShift

Rotate Redshift cluster master password:

- Updates cluster configuration
- Tests connection with new password

### DocumentDB

Rotate DocumentDB database credentials:

- Similar to RDS rotation
- Handles connection pooling

---

## Custom Rotation

For non-AWS or custom systems:

**Build custom Lambda rotation function:**

1. **Create secret**: Store credentials in JSON format
2. **Create Lambda function**: Implement rotation logic
3. **Set rotation schedule**: Configure how often to rotate
4. **Lambda permissions**: Grant Secrets Manager permission to invoke

**Lambda function steps:**
```
1. Create new secret in target system
2. Set new credentials in Secrets Manager (AWSPENDING)
3. Test connection with new credentials
4. Finish rotation (AWSPENDING → AWSCURRENT)
5. Clean up old credentials (optional)
```

---

## Encryption

All secrets are encrypted at rest:

### Default Encryption

- Encrypted using AWS KMS
- Default AWS managed key (aws/secretsmanager)
- No additional cost
- Automatic key rotation by AWS

### Custom KMS Key

- Use customer-managed KMS key for encryption
- More control and audit capabilities
- Comply with specific regulatory requirements
- Additional KMS costs apply

---

## Access Control (IAM Integration)

Control who can access secrets with IAM policies:

### Policy Example

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "AWS": "arn:aws:iam::123456789012:role/MyAppRole"
      },
      "Action": [
        "secretsmanager:GetSecretValue",
        "secretsmanager:DescribeSecret"
      ],
      "Resource": "arn:aws:secretsmanager:us-east-1:123456789012:secret:prod/db/*"
    }
  ]
}
```

**Key permissions:**
- **GetSecretValue**: Retrieve secret value
- **DescribeSecret**: Get secret metadata
- **PutSecretValue**: Update secret
- **RotateSecret**: Initiate rotation
- **ListSecrets**: List all secrets
- **DeleteSecret**: Delete secret

---

## Audit and Monitoring

### CloudTrail Integration

All API calls to Secrets Manager are logged:

- Who accessed which secret
- When the access occurred
- From where (IP address)
- What action was performed (get, update, rotate)

### CloudWatch

Monitor secret usage:

- Create alarms for suspicious access patterns
- Track rotation success/failure
- Monitor API call rates

### EventBridge

Trigger actions on secret events:

- Secret rotation completed
- Secret access denied
- Secret updated
- Trigger Lambda, SNS, or other services

---

## Secrets Manager vs Parameter Store

| Feature | Secrets Manager | Parameter Store |
|---------|-----------------|-----------------|
| **Purpose** | Complex secrets | Configuration and simple secrets |
| **Rotation** | Built-in automatic rotation | Manual rotation |
| **Cost** | $0.40/secret/month + API calls | Free (within limits) |
| **Encryption** | Always encrypted with KMS | Can be encrypted (optional) |
| **Complexity** | More features | Simpler |
| **Use case** | Database credentials, API keys | Config values, feature flags |
| **Versioning** | Advanced (rotation versions) | Simple versioning |
| **TTL** | Not applicable | Supported |

**Choose Secrets Manager for:** Frequently rotated credentials, audit requirements  
**Choose Parameter Store for:** Configuration, static values, cost optimization

---

## Secret Naming Best Practices

Use hierarchical naming for organization:

```
prod/database/mysql/password
prod/database/mysql/username
dev/api/third-party/key
staging/oauth/refresh-token
prod/app/config/secret
```

Benefits:
- Organized structure
- Easier to manage related secrets
- Clearer access control policies

---

## Secrets Manager Best Practices

### 1. Principle of Least Privilege

- Grant minimal required permissions
- Use resource-based policies
- Separate secrets by environment

### 2. Regular Rotation

- Enable automatic rotation where possible
- Minimum 30 days for passwords (best practice: 7-14 days)
- Test rotation thoroughly before production use

### 3. Encryption

- Use customer-managed KMS keys for sensitive secrets
- Separate keys for different environments
- Monitor KMS key usage

### 4. Monitoring and Auditing

- Enable CloudTrail logging
- Set up CloudWatch alarms for suspicious access
- Regularly audit secret access logs
- Alert on failed rotations

### 5. Access Control

- Use IAM roles, not root credentials
- Separate roles for different applications
- Resource-based policies for cross-account access

### 6. Application Integration

- Use AWS SDK to retrieve secrets (not hardcoded)
- Cache secrets with appropriate TTL
- Handle rotation-related retries gracefully
- Don't log secrets in application logs

### 7. High Availability

- Secrets are replicated across AZs automatically
- No single point of failure
- Instant availability in case of AZ failure

### 8. Compliance

- Use CloudTrail for audit trail
- Encryption for regulatory compliance (HIPAA, PCI-DSS, etc.)
- Secret versioning for compliance history
- Automated rotation for compliance requirements

---

## Common Use Cases

### 1. Database Credentials

Store and rotate RDS database credentials:

- Automatic password rotation
- Multiple applications can share credentials
- Audit trail of access

### 2. API Keys Management

Centralize third-party API keys:

- Slack, GitHub, PagerDuty tokens
- Easier key rotation
- Revoke compromised keys quickly

### 3. Application Configuration Secrets

Store secrets needed by applications:

- Database connection strings
- Encryption keys
- OAuth secrets

### 4. Multi-Environment Secrets

Separate secrets for prod, staging, dev:

- Environment-specific credentials
- Different rotation policies
- Access control per environment

### 5. Cross-Account Access

Share secrets between AWS accounts:

- Service account credentials
- Assume role credentials
- Resource-based policies

---

## Integration with AWS Services

### RDS/Aurora

Automatic rotation of database passwords:

- Define rotation schedule
- Aurora and RDS update password automatically
- Zero downtime password changes

### Lambda

Retrieve secrets in Lambda functions:

```python
import json
import boto3

def lambda_handler(event, context):
    client = boto3.client('secretsmanager')
    response = client.get_secret_value(SecretId='my-secret')
    secret = json.loads(response['SecretString'])
    # Use secret in application
```

### ECS/EKS

Pass secrets to containers:

**ECS Task Definition:**
```json
{
  "name": "DB_PASSWORD",
  "valueFrom": "arn:aws:secretsmanager:region:account:secret:db-password"
}
```

### CloudFormation

Create and manage secrets:

```yaml
MySecret:
  Type: AWS::SecretsManager::Secret
  Properties:
    Name: prod/db/password
    SecretString: !Sub |
      {
        "username": "admin",
        "password": "${DBPassword}"
      }
```

---

## Pricing

### Cost Structure

- **Secret storage**: $0.40 per secret per month
- **API calls**: $0.06 per 10,000 API calls
- **Automatic rotation**: Included (Lambda costs may apply)

### Cost Optimization

- Consolidate related secrets into JSON objects
- Cache secrets in applications
- Use Parameter Store for configuration (free tier available)
- Minimize unnecessary API calls

---

## Getting Started

1. Create a secret in AWS Secrets Manager
2. Store database credentials, API keys, or other sensitive data
3. Configure IAM policies to restrict access
4. Set up automatic rotation (if applicable)
5. Integrate with applications using AWS SDK
6. Enable CloudTrail for auditing
7. Set up monitoring and alarms
8. Test rotation and failover scenarios
