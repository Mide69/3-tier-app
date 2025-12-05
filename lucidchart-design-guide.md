# Step-by-Step Lucidchart Design Guide
## DWP 3-Tier Architecture - Beginner Friendly

### Prerequisites
- Lucidchart account (free or paid)
- Basic understanding of cloud concepts
- Access to AWS icon library in Lucidchart

## Phase 1: Setup and Foundation (15 minutes)

### Step 1: Create New Document
1. **Login to Lucidchart** → Click "Create New Document"
2. **Select Template** → Choose "AWS Architecture" or "Blank Document"
3. **Page Setup** → Set to A3 Landscape for better space
4. **Import AWS Icons** → Go to "Shapes" → Search "AWS" → Import AWS icon library

### Step 2: Create Base Layout Structure
1. **Draw Main Container** → Use Rectangle tool → Create large box labeled "AWS Cloud"
2. **Add Internet Symbol** → Place cloud icon at top → Label "Internet"
3. **Create VPC Container** → Large rectangle inside AWS Cloud → Label "VPC (10.0.0.0/16)"

## Phase 2: Network Foundation (20 minutes)

### Step 3: Design Availability Zones
1. **Create 3 AZ Columns** → Draw 3 vertical rectangles inside VPC
2. **Label AZs** → "eu-west-2a", "eu-west-2b", "eu-west-2c"
3. **Color Code** → Use light blue for AZ backgrounds

### Step 4: Add Public Subnets
1. **Draw Public Subnet Row** → Horizontal rectangle across all 3 AZs (top section)
2. **Label** → "Public Subnets (10.0.1.0/24, 10.0.2.0/24, 10.0.3.0/24)"
3. **Color** → Light green background
4. **Add Components per AZ:**
   - Internet Gateway (IGW) icon - place at top center
   - Application Load Balancer (ALB) icon in each AZ
   - NAT Gateway icon in each AZ

### Step 5: Add Private Subnets (3 Tiers)
1. **Web Tier Subnets** → Row below public subnets
   - Label: "Web Private Subnets (10.0.10.0/24, 10.0.11.0/24, 10.0.12.0/24)"
   - Color: Light orange

2. **App Tier Subnets** → Row below web tier
   - Label: "App Private Subnets (10.0.20.0/24, 10.0.21.0/24, 10.0.22.0/24)"
   - Color: Light yellow

3. **Data Tier Subnets** → Bottom row
   - Label: "Data Private Subnets (10.0.30.0/24, 10.0.31.0/24, 10.0.32.0/24)"
   - Color: Light purple

## Phase 3: Security and CDN Layer (15 minutes)

### Step 6: Add Security and CDN
1. **CloudFront** → Place above Internet → Use CloudFront icon
2. **AWS Shield + WAF** → Place between CloudFront and ALB → Use shield icon
3. **Route 53** → Place next to CloudFront → Use Route 53 icon

### Step 7: Add Security Groups (Visual Representation)
1. **Create Security Group Boxes** → Dotted line rectangles around each tier
2. **Label Each SG:**
   - "ALB-SG (80,443 from 0.0.0.0/0)"
   - "Web-SG (80 from ALB-SG)"
   - "App-SG (8080 from Web-SG)"
   - "DB-SG (3306,6379 from App-SG)"

## Phase 4: Tier 1 - Web Microservices (20 minutes)

### Step 8: Design Web Tier Components
1. **Add ECS Cluster** → Place ECS cluster icon spanning all web AZs
   - Label: "ECS Cluster - Web Tier"

2. **Add ECS Services** in each Web AZ:
   - Use ECS service icon (container with gear)
   - Label: "Frontend ECS Service (Fargate)"
   - Add "React/Angular SPA" text below
   - Show 2-3 task instances per service

3. **Add ECS Auto Scaling** → Dotted box around all web services
   - Label: "ECS Service Auto Scaling (2-10 tasks)"

4. **Add Internal ALB** → ALB icon between web and app tiers
   - Label: "Internal ALB → API Gateway"

### Step 9: Add Static Assets
1. **S3 Bucket** → Place to the side of web tier
   - Use S3 icon
   - Label: "S3 Static Assets (CSS, JS, Images)"
2. **Connect to CloudFront** → Draw arrow from S3 to CloudFront

## Phase 5: Tier 2 - DWP Application Microservices (25 minutes)

### Step 10: Add API Gateway Layer
1. **Primary API Gateway** → Place API Gateway icon at app tier entry
   - Label: "API Gateway - DWP Services"
   - Position between Internal ALB and ECS services

2. **External API Gateway** → Place second API Gateway icon
   - Label: "API Gateway - External APIs"
   - Position for government API integrations

### Step 11: Design DWP ECS Microservices
**ECS Cluster spanning all AZs:**
1. **Add ECS Cluster** → Large container across app tier
   - Label: "ECS Cluster - Application Tier"

**In AZ-A:**
1. **Citizen Service** → ECS service icon → "Citizen API (ECS Fargate)"
2. **Identity Service** → ECS service icon → "Identity API (ECS Fargate)"

**In AZ-B:**
1. **Benefits Service** → ECS service icon → "Benefits API (ECS Fargate)"
2. **Claims Service** → ECS service icon → "Claims API (ECS Fargate)"

**In AZ-C:**
1. **Address Service** → ECS service icon → "Address API (ECS Fargate)"
2. **Notification Service** → ECS service icon → "Notification API (ECS Fargate)"

### Step 12: Connect API Gateway to ECS Services
1. **Service Discovery** → Place AWS Cloud Map icon in ECS cluster
2. **API Routes** → Draw lines from API Gateway to each ECS service:
   - `/citizen/*` → Citizen Service
   - `/benefits/*` → Benefits Service
   - `/claims/*` → Claims Service
   - `/address/*` → Address Service
3. **Load Balancing** → Show ALB distributing to ECS services

## Phase 6: Tier 3 - Data Layer (20 minutes)

### Step 12: Design Database Components
**In AZ-A:**
1. **RDS Primary** → RDS icon → Label "RDS MySQL Primary"
2. **DynamoDB** → DynamoDB icon → Label "DynamoDB Sessions"

**In AZ-B:**
1. **RDS Standby** → RDS icon → Label "RDS MySQL Standby"
2. **DynamoDB Global Tables** → DynamoDB icon → Label "DynamoDB Global Tables"

**In AZ-C:**
1. **ElastiCache** → ElastiCache icon → Label "ElastiCache Redis"
2. **S3 Data Buckets** → S3 icon → Label "S3 Data & Backups"

### Step 13: Add Database Connections
1. **Replication Arrow** → Draw arrow from Primary to Standby RDS
2. **Read Replica Connections** → Show read connections from app tier
3. **Cache Connections** → Connect app services to ElastiCache

## Phase 7: Supporting Services (15 minutes)

### Step 14: Container Orchestration Services
**Create separate section below main architecture:**
1. **ECS Cluster** → ECS icon → "ECS Cluster Management"
2. **ECR** → ECR icon → "Container Registry"
3. **Service Discovery** → Cloud Map icon → "Service Discovery"
4. **X-Ray** → X-Ray icon → "Distributed Tracing"

### Step 15: Monitoring and Management
1. **CloudWatch** → CloudWatch icon → "Container Insights"
2. **CloudTrail** → CloudTrail icon → "Audit Logging"
3. **Config** → Config icon → "Compliance Monitoring"
4. **Systems Manager** → Systems Manager icon → "Parameter Store"

## Phase 8: External APIs (15 minutes)

### Step 16: Government API Integration
**Create external section:**
1. **Royal Mail API** → External service box → "Address Validation"
2. **HMRC APIs** → External service box → "Tax & Employment Data"
3. **NHS APIs** → External service box → "Health Records"
4. **GOV.UK Verify** → External service box → "Identity Verification"

### Step 17: Connect External APIs
1. **API Gateway Connections** → Draw lines from External API Gateway to each external service
2. **Security Annotations** → Add "OAuth 2.0", "API Keys", "mTLS" labels on connections

## Phase 9: Security Implementation (20 minutes)

### Step 18: Add Security Services
1. **KMS** → KMS icon → "Encryption Key Management"
2. **Secrets Manager** → Secrets Manager icon → "Database Credentials"
3. **IAM** → IAM icon → "Roles & Policies"
4. **GuardDuty** → GuardDuty icon → "Threat Detection"

### Step 19: Security Boundaries
1. **VPC Endpoints** → Add VPC endpoint icons for AWS services
2. **Security Group Rules** → Add detailed annotations:
   ```
   Web-SG: Port 80 from ALB-SG only
   App-SG: Port 8080 from Web-SG only
   DB-SG: Port 3306 from App-SG only
   ```

## Phase 10: Data Flow and Scaling (15 minutes)

### Step 20: Add Data Flow Arrows
1. **User Journey** → Thick blue arrows showing:
   - Internet → CloudFront → WAF → ALB → Web Tier
   - Web Tier → Internal ALB → App Tier
   - App Tier → Database Tier

2. **External API Flow** → Green arrows showing:
   - App Services → API Gateway → External APIs

### Step 21: Auto Scaling Annotations
1. **Scaling Triggers** → Add text boxes with:
   ```
   Web Tier: CPU > 70%, Memory > 80%
   App Tier: Request Count > 1000/min
   Database: Read Replicas for scaling
   ```

2. **High Availability** → Add annotations:
   ```
   Multi-AZ: 99.99% availability
   Auto Failover: RDS 60-120 seconds
   Load Balancing: Cross-AZ distribution
   ```

## Phase 11: Final Touches (10 minutes)

### Step 22: Add Legend and Labels
1. **Create Legend Box** → Bottom right corner
2. **Color Coding:**
   - Green: Public Subnets
   - Orange: Web Tier
   - Yellow: App Tier
   - Purple: Data Tier
   - Blue: Supporting Services

3. **Icon Legend** → Show what each AWS icon represents

### Step 23: Add Compliance and Standards
1. **Compliance Box** → Top right corner:
   ```
   ✓ GDPR Compliant
   ✓ Cyber Essentials Plus
   ✓ ISO 27001
   ✓ Government Security Standards
   ```

2. **Architecture Principles** → Top left corner:
   ```
   ✓ Scalable (Auto Scaling)
   ✓ Available (Multi-AZ)
   ✓ Secure (Defense in Depth)
   ✓ Cost Optimized
   ```

## Quality Checklist

### Before Finalizing:
- [ ] All components are properly labeled
- [ ] Data flow is clear and logical
- [ ] Security boundaries are visible
- [ ] High availability is demonstrated
- [ ] Scalability mechanisms are shown
- [ ] External API integrations are clear
- [ ] Color coding is consistent
- [ ] Legend explains all symbols
- [ ] Compliance requirements are noted
- [ ] Architecture fits on one page

### Pro Tips:
1. **Use Consistent Spacing** → Align all components properly
2. **Group Related Services** → Use containers/boxes to group services
3. **Clear Labeling** → Every component should have a clear, descriptive label
4. **Color Psychology** → Use colors that make sense (red for security, blue for data)
5. **Arrows Matter** → Show direction of data flow clearly
6. **Keep It Simple** → Don't overcrowd - use multiple diagrams if needed

## Interview Presentation Tips

### Key Points to Highlight:
1. **Multi-AZ Design** → Point out redundancy across 3 AZs
2. **Microservices Benefits** → Explain independent scaling and deployment
3. **Security Layers** → Walk through defense in depth approach
4. **Government Integration** → Highlight DWP-specific external APIs
5. **Cost Optimization** → Mention Fargate, auto scaling, reserved instances
6. **Monitoring Strategy** → Show comprehensive observability

### Expected Questions:
- "How does this handle peak traffic?" → Point to auto scaling
- "What happens if one AZ fails?" → Explain multi-AZ failover
- "How do you secure citizen data?" → Walk through security layers
- "How do microservices communicate?" → Explain service mesh and API Gateway