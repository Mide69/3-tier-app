# AI Prompt for 3-Tier AWS Architecture Diagram

## Prompt for AI Diagram Generation Tools (Claude, ChatGPT, Midjourney, etc.)

```
Create a professional AWS 3-tier architecture diagram with the following specifications:

ARCHITECTURE OVERVIEW:
- 3-tier web application hosted on AWS
- Multi-AZ deployment across 3 availability zones
- Scalable, secure, and highly available design

TIER 1 - PRESENTATION LAYER:
- Amazon CloudFront CDN at the top
- AWS Shield + WAF for security
- Application Load Balancer in public subnets
- Web servers (EC2/ECS) in private subnets across 3 AZs
- Auto Scaling Groups for horizontal scaling

TIER 2 - APPLICATION LAYER:
- Internal Application Load Balancer
- Application servers (EC2/ECS/Lambda) in private subnets
- Distributed across 3 availability zones
- Auto Scaling Groups with custom metrics

TIER 3 - DATA LAYER:
- RDS MySQL Multi-AZ (Primary in AZ-A, Standby in AZ-B)
- RDS Read Replicas in AZ-A and AZ-B
- ElastiCache Redis cluster in AZ-C
- S3 buckets for object storage

NETWORK ARCHITECTURE:
- VPC with public and private subnets
- Internet Gateway for public access
- NAT Gateways in each AZ for outbound traffic
- Security Groups and NACLs for network security

SUPPORTING SERVICES (show as separate section):
- Monitoring: CloudWatch, X-Ray
- Security: GuardDuty, Config, IAM, KMS, Secrets Manager
- Management: CloudTrail, OpenSearch, Backup Service
- DevOps: CodePipeline, Route53

VISUAL REQUIREMENTS:
- Clean, professional diagram style
- Use official AWS service icons
- Show data flow arrows between tiers
- Color-code different subnet types (public=green, private=blue)
- Include security boundaries and access controls
- Label all components clearly
- Show multi-AZ redundancy visually

STYLE PREFERENCES:
- Modern, enterprise-grade appearance
- Suitable for executive presentation
- High contrast and readable
- Include legend for icons and colors
```

## Alternative Prompt for Diagramming Tools (Lucidchart, Draw.io, etc.)

```
Design a comprehensive AWS 3-tier architecture showing:

LAYOUT: Top-down flow from Internet → CloudFront → VPC with 3 horizontal tiers

COMPONENTS TO INCLUDE:
✓ Internet cloud symbol at top
✓ CloudFront CDN with global edge locations
✓ AWS Shield + WAF security layer
✓ VPC container with labeled subnets
✓ 3 Availability Zones (AZ-A, AZ-B, AZ-C) shown vertically
✓ Public subnets with ALB and NAT Gateways
✓ Private subnets for each tier (Web, App, Database)
✓ RDS Multi-AZ with primary/standby configuration
✓ ElastiCache and S3 services
✓ Supporting services panel at bottom

CONNECTIONS:
- Bidirectional arrows between tiers
- Security group boundaries around each tier
- Load balancer distribution to multiple instances
- Database replication connections

ANNOTATIONS:
- Subnet CIDR blocks (10.0.x.0/24)
- Security group rules summary
- Auto scaling triggers and thresholds
- High availability features highlighted
```

## Prompt for Code-Based Diagram Tools (Mermaid, PlantUML)

```
Generate a Mermaid or PlantUML diagram code for AWS 3-tier architecture featuring:

STRUCTURE:
- Internet → CloudFront → WAF → VPC
- 3 AZs with public/private subnet pairs
- Web tier → App tier → Database tier flow
- Load balancers between each tier
- Multi-AZ RDS with read replicas
- Supporting AWS services cluster

SYNTAX REQUIREMENTS:
- Use proper AWS service names
- Include subnet relationships
- Show security group boundaries
- Indicate auto scaling groups
- Display data flow directions
- Add monitoring and management services

OUTPUT: Clean, executable diagram code that renders a professional AWS architecture
```