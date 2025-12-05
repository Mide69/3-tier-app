# ECS + API Gateway Architecture Design
## DWP Microservices with Centralized API Management

### Architecture Overview

```
Internet → CloudFront → WAF → ALB → ECS Web Services
                                ↓
                        API Gateway (Primary)
                                ↓
                    ECS Application Microservices
                                ↓
                        API Gateway (External)
                                ↓
                        Government APIs
```

## ECS Service Architecture

### Web Tier - ECS Services
```yaml
ECS Cluster: dwp-web-cluster
Services:
  - frontend-service:
      Platform: Fargate
      Tasks: 2-10 (auto-scaling)
      CPU: 512
      Memory: 1024
      Port: 80
      Health Check: /health
```

### Application Tier - ECS Microservices
```yaml
ECS Cluster: dwp-app-cluster
Services:
  - citizen-api:
      Platform: Fargate
      Tasks: 2-6 per AZ
      CPU: 256
      Memory: 512
      Port: 8080
      Routes: /citizen/*
      
  - benefits-api:
      Platform: Fargate
      Tasks: 3-9 per AZ
      CPU: 512
      Memory: 1024
      Port: 8080
      Routes: /benefits/*
      
  - claims-api:
      Platform: Fargate
      Tasks: 2-8 per AZ
      CPU: 512
      Memory: 1024
      Port: 8080
      Routes: /claims/*
      
  - address-api:
      Platform: Fargate
      Tasks: 1-4 per AZ
      CPU: 256
      Memory: 512
      Port: 8080
      Routes: /address/*
      
  - identity-api:
      Platform: Fargate
      Tasks: 2-6 per AZ
      CPU: 256
      Memory: 512
      Port: 8080
      Routes: /identity/*
```

## API Gateway Configuration

### Primary API Gateway - Internal Services
```yaml
Name: dwp-internal-api
Type: Regional
Authentication: Cognito User Pool
Authorization: IAM + Resource Policies

Routes:
  /citizen/{proxy+}:
    Integration: ALB (ECS Citizen Service)
    Method: ANY
    Auth: Required
    
  /benefits/{proxy+}:
    Integration: ALB (ECS Benefits Service)
    Method: ANY
    Auth: Required
    
  /claims/{proxy+}:
    Integration: ALB (ECS Claims Service)
    Method: ANY
    Auth: Required
    
  /address/{proxy+}:
    Integration: ALB (ECS Address Service)
    Method: ANY
    Auth: Required
    
  /identity/{proxy+}:
    Integration: ALB (ECS Identity Service)
    Method: ANY
    Auth: Required

Throttling:
  Rate: 10000 requests/second
  Burst: 5000 requests

Caching:
  TTL: 300 seconds
  Keys: Authorization header, query parameters
```

### External API Gateway - Government APIs
```yaml
Name: dwp-external-api
Type: Regional
Authentication: API Keys + OAuth 2.0

Routes:
  /external/royal-mail/{proxy+}:
    Integration: HTTP (Royal Mail API)
    Method: GET, POST
    Auth: API Key
    Rate Limit: 1000/min
    
  /external/hmrc/{proxy+}:
    Integration: HTTP (HMRC APIs)
    Method: GET, POST
    Auth: OAuth 2.0
    Rate Limit: 500/min
    
  /external/nhs/{proxy+}:
    Integration: HTTP (NHS APIs)
    Method: GET
    Auth: mTLS + OAuth 2.0
    Rate Limit: 200/min
    
  /external/gov-verify/{proxy+}:
    Integration: HTTP (GOV.UK Verify)
    Method: POST
    Auth: SAML 2.0
    Rate Limit: 100/min

Circuit Breaker:
  Failure Threshold: 50%
  Timeout: 30 seconds
  Retry: 3 attempts with exponential backoff
```

## ECS Service Discovery

### AWS Cloud Map Configuration
```yaml
Namespace: dwp.local
Services:
  - citizen-api.dwp.local
  - benefits-api.dwp.local
  - claims-api.dwp.local
  - address-api.dwp.local
  - identity-api.dwp.local

Health Checks:
  Type: HTTP
  Path: /health
  Interval: 30 seconds
  Failure Threshold: 3
```

## Load Balancing Strategy

### Application Load Balancer (ALB)
```yaml
Public ALB:
  Name: dwp-public-alb
  Scheme: internet-facing
  Listeners:
    - Port: 443 (HTTPS)
    - Port: 80 (HTTP → HTTPS redirect)
  Target Groups:
    - ECS Web Services (port 80)

Internal ALB:
  Name: dwp-internal-alb
  Scheme: internal
  Listeners:
    - Port: 80 (HTTP)
  Target Groups:
    - ECS Citizen Service (port 8080)
    - ECS Benefits Service (port 8080)
    - ECS Claims Service (port 8080)
    - ECS Address Service (port 8080)
    - ECS Identity Service (port 8080)

Health Checks:
  Path: /health
  Interval: 30 seconds
  Healthy Threshold: 2
  Unhealthy Threshold: 5
```

## Auto Scaling Configuration

### ECS Service Auto Scaling
```yaml
Scaling Policies:
  Target Tracking:
    - Metric: CPU Utilization
      Target: 70%
    - Metric: Memory Utilization
      Target: 80%
    - Metric: ALB Request Count
      Target: 1000 requests/minute

  Step Scaling:
    Scale Out:
      - CPU > 80%: Add 2 tasks
      - CPU > 90%: Add 4 tasks
    Scale In:
      - CPU < 30%: Remove 1 task
      - CPU < 20%: Remove 2 tasks

  Scheduled Scaling:
    Business Hours (9 AM - 6 PM):
      Min Capacity: 4 tasks
      Max Capacity: 20 tasks
    Off Hours:
      Min Capacity: 2 tasks
      Max Capacity: 10 tasks
```

### API Gateway Auto Scaling
```yaml
# API Gateway automatically scales
# No configuration needed - serverless
Concurrent Executions: 1000 (default)
Throttling: Automatic based on account limits
```

## Security Implementation

### ECS Task Security
```yaml
Task Roles:
  citizen-task-role:
    Policies:
      - DynamoDBReadWrite (citizen tables)
      - SecretsManagerRead (DB credentials)
      - CloudWatchLogs
      
  benefits-task-role:
    Policies:
      - RDSConnect (benefits database)
      - SQSPublish (notification queue)
      - SecretsManagerRead
      
  claims-task-role:
    Policies:
      - RDSConnect (claims database)
      - S3Read (document storage)
      - SecretsManagerRead

Network Security:
  - awsvpc network mode for task isolation
  - Security groups per service
  - No public IP addresses for tasks
```

### API Gateway Security
```yaml
Authentication:
  Internal API:
    - Cognito User Pool
    - IAM roles for service-to-service
    
  External API:
    - API Keys for rate limiting
    - OAuth 2.0 for government APIs
    - mTLS for sensitive endpoints

Authorization:
  - Resource-based policies
  - Method-level permissions
  - Request validation
  - Response filtering

WAF Integration:
  - SQL injection protection
  - XSS prevention
  - Rate limiting per IP
  - Geographic restrictions
```

## Monitoring and Observability

### ECS Monitoring
```yaml
CloudWatch Container Insights:
  - CPU and memory utilization per task
  - Network I/O metrics
  - Task count and health status
  
Custom Metrics:
  - Application response times
  - Business logic metrics
  - Error rates per service
  
Alarms:
  - High CPU utilization (>80%)
  - Memory utilization (>85%)
  - Task failure rate (>5%)
  - Service unavailability
```

### API Gateway Monitoring
```yaml
CloudWatch Metrics:
  - Request count and latency
  - Error rates (4xx, 5xx)
  - Cache hit/miss ratios
  - Throttling events
  
X-Ray Tracing:
  - End-to-end request tracing
  - Service map visualization
  - Performance bottleneck identification
  - Error root cause analysis
  
Custom Dashboards:
  - API performance overview
  - Service health status
  - External API dependency health
```

## Cost Optimization

### ECS Cost Management
```yaml
Fargate Spot:
  - Use for non-critical workloads
  - 70% cost savings
  - Automatic failover to on-demand
  
Right Sizing:
  - Monitor CPU/memory usage
  - Adjust task definitions based on metrics
  - Use smaller task sizes where possible
  
Reserved Capacity:
  - Fargate Savings Plans for predictable workloads
  - 20% savings with 1-year commitment
```

### API Gateway Cost Optimization
```yaml
Caching:
  - Enable caching for read-heavy endpoints
  - Reduce backend calls by 80%
  - TTL optimization based on data freshness
  
Request Optimization:
  - Compress responses
  - Minimize payload sizes
  - Batch API calls where possible
  
Usage Plans:
  - Tier-based pricing for different user types
  - Rate limiting to prevent cost spikes
```

## Deployment Strategy

### Blue/Green Deployment
```yaml
ECS Services:
  - Use ECS service updates
  - Gradual task replacement
  - Health check validation
  - Automatic rollback on failure
  
API Gateway:
  - Stage-based deployments
  - Canary releases (10% traffic)
  - Gradual traffic shifting
  - Rollback capability
```

### CI/CD Pipeline
```yaml
Source: GitHub/CodeCommit
Build: CodeBuild
  - Docker image build
  - Security scanning
  - Unit tests
  
Deploy: CodeDeploy
  - ECS service updates
  - API Gateway stage promotion
  - Automated testing
  - Production deployment
```