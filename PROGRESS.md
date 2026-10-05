# Progress Tracker - AWS Serverless Order Pipeline

**Overall Progress:** 2/6 MVP resources complete | **Current Step:** Step 2 - Lambda IAM Role | **Last Updated:** 2026-10-05 | **Status:** 🔄 In Progress

---

## 📊 Executive Summary

This project builds an **event-driven serverless order processing pipeline** using AWS services with Infrastructure-as-Code (CloudFormation) templates.

### Project Architecture

```text
Order Submission (API Gateway)
    ↓
Lambda Validator
    ↓
DynamoDB + DynamoDB Streams
    ↓
EventBridge Pipes (Conditional Routing)
    ↓
SQS Queue → Lambda Processor → SNS → CloudWatch
    ↓
Dead-Letter Queue (Failed Orders)
```

### MVP: 5-Step Implementation Plan (Estimated: ~3 hours)

**Step 1** ⏳ DynamoDB Orders Table with Streams  
**Step 2** ⏳ Lambda IAM Execution Role  
**Step 3** ⏳ CreateOrder Lambda Function  
**Step 4** ⏳ API Gateway HTTP API  
**Step 5** ⏳ Frontend Order Submission App  

### Phase 2: Extended Features (After MVP Complete)

- Order Processor Lambda (async processing)
- SQS queues + Dead-Letter Queue
- EventBridge Pipes (conditional routing)
- SNS notifications
- CloudWatch monitoring (alarms, dashboards)

---

## ✅ Completed Work

| # | Component | Status | Details |
| --- | --- | --- | --- |
| 1 | Repository & Nested Stack Pattern | ✅ | Base structure, CI/CD pipeline configured |
| 2 | S3 Bucket Template | ✅ | `templates/s3-bucket.yaml` - reusable nested template |
| 3 | Parameter Framework | ✅ | `cloudformation/parameters.json` - environment-specific config |
| 4 | CI/CD Pipeline | ✅ | `.github/workflows/ci.yaml` - AWS OIDC validation & deployment |
| 5 | Deployment Scripts | ✅ | Template upload and stack management automation |
| 6 | Project Documentation | ✅ | CLAUDE.md, README.md - architecture and conventions |

---

## 📋 5-Step MVP Resource Status

| Step | AWS Service | Component | Status | File | Dependencies |
| --- | --- | --- | --- | --- | --- |
| 0️⃣ | S3 | Bucket Template (Foundation) | ✅ Complete | `templates/s3-bucket.yaml` | None |
| 1️⃣ | DynamoDB | Orders Table + Streams | ✅ Complete | `cloudformation/template.yaml` (nested stack) | None |
| 2️⃣ | IAM | Lambda Execution Role | 🔄 Next | `templates/iam-lambda-role.yaml` | None |
| 3️⃣ | Lambda | CreateOrder Function | ⏳ Pending | `templates/lambda-create-order.yaml` + `lambda/create-order/index.js` | Step 1, 2 |
| 4️⃣ | API Gateway | HTTP API + /orders endpoint | ⏳ Pending | `templates/api-gateway-http.yaml` | Step 3 |
| 5️⃣ | Frontend | Order Submission App | ⏳ Pending | `frontend/index.html` (or React) | Step 4 |

### Phase 2 Resources (After MVP)

| # | AWS Service | Component | Status | Dependencies |
| --- | --- | --- | --- | --- |
| 6 | Lambda | Order Processor Function | 🔴 Pending | Step 1, 2, 3 |
| 7 | Lambda | Error Handler Function | 🔴 Pending | Step 1, 2 |
| 8 | SQS | Processing Queue | 🔴 Pending | None |
| 9 | SQS | Dead-Letter Queue | 🔴 Pending | None |
| 10 | EventBridge Pipes | Order Routing | 🔴 Pending | Step 1, 8 |
| 11 | SNS | Notifications Topic | 🔴 Pending | None |
| 12 | CloudWatch | Logs, Metrics, Alarms | 🔴 Pending | None |

---

## 🎯 Current Phase: MVP Core Implementation (5-Step Roadmap)

### What's Done

- [x] Repository initialized with nested template pattern
- [x] S3 bucket template with security defaults (versioning, public access blocking)
- [x] CI/CD pipeline with AWS OIDC for keyless authentication
- [x] Parameter framework for multi-environment deployments
- [x] Project documentation and architectural guidance

### Implementation Roadmap (5 Steps)

#### Step 1: Create DynamoDB Table with Streams ✅ COMPLETE
- [x] DynamoDB root template (`cloudformation/template.yaml`)
  - [x] Orders table schema (OrderID PK, Timestamp SK, CustomerID GSI)
  - [x] DynamoDB Streams enabled (NEW_AND_OLD_IMAGES)
  - [x] Billing mode: PAY_PER_REQUEST (on-demand for dev/staging)
  - [x] Environment-specific parameter files (devl, stag, prod)
  - [x] PITR (Point-in-Time Recovery) enabled for data protection
  - [x] Nested stack pattern - references cfn-nested-aws-dynamodb-table GitHub repo

#### Step 2: Create IAM Role for Lambda ⏳
- [ ] IAM role template (`templates/iam-lambda-role.yaml`)
  - [ ] Lambda execution role
  - [ ] Permissions: DynamoDB read/write
  - [ ] Permissions: CloudWatch logs
  - [ ] Permissions: DynamoDB Streams read
  - [ ] Trust relationship: Lambda service

#### Step 3: Create "CreateOrder" Lambda Function ⏳
- [ ] Lambda function template (`templates/lambda-create-order.yaml`)
  - [ ] Function code (validates & stores order in DynamoDB)
  - [ ] Environment variables: Orders table name
  - [ ] Role attachment: IAM Lambda role
  - [ ] Timeout: 30 seconds
  - [ ] Memory: 256 MB
  - [ ] CloudWatch logs enabled

#### Step 4: Create API Gateway HTTP API ⏳
- [ ] API Gateway template (`templates/api-gateway-http.yaml`)
  - [ ] HTTP API (not REST API for lower latency/cost)
  - [ ] POST /orders endpoint
  - [ ] Lambda integration (CreateOrder function)
  - [ ] Request validation (schema)
  - [ ] Response models (success/error)
  - [ ] CORS configuration (if needed)
  - [ ] CloudWatch logging enabled

#### Step 5: Build Frontend Order Submission App ⏳
- [ ] Frontend application repository/folder
  - [ ] HTML/React form for order submission
  - [ ] Call API Gateway POST /orders endpoint
  - [ ] Display order confirmation with OrderID
  - [ ] Error handling and user feedback
  - [ ] S3 hosting (static website)
  - [ ] CloudFront CDN distribution (optional)

---

## 🚀 Detailed Implementation Plan (Following 5-Step Roadmap)

### Step 1️⃣: DynamoDB Orders Table with Streams

**Deliverable**: `templates/dynamodb-orders-table.yaml`

**Configuration**:
```yaml
Table: Orders
  PrimaryKey:
    - OrderID (Partition Key) - String
    - Timestamp (Sort Key) - String (ISO 8601)
  Attributes:
    - CustomerID (Global Secondary Index)
    - Status (tracked field)
    - Items (JSON array)
  StreamSpecification:
    - Type: NEW_AND_OLD_IMAGES
    - Retention: 24 hours
  BillingMode: PAY_PER_REQUEST
```

**Dependencies**: None (foundation)

**Next Step**: Step 2 (IAM Role needs this table name)

---

### Step 2️⃣: IAM Lambda Execution Role

**Deliverable**: `templates/iam-lambda-role.yaml`

**Permissions**:
```
DynamoDB:
  - dynamodb:PutItem (Orders table write)
  - dynamodb:GetItem (Orders table read)
  - dynamodb:UpdateItem (Orders table update)
  - dynamodb:Query (CustomerID GSI queries)

DynamoDB Streams:
  - dynamodb:GetRecords
  - dynamodb:GetShardIterator
  - dynamodb:DescribeStream
  - dynamodb:ListStreams
  - dynamodb:ListTables

CloudWatch:
  - logs:CreateLogGroup
  - logs:CreateLogStream
  - logs:PutLogEvents
```

**Trust Relationship**: Lambda service (`lambda.amazonaws.com`)

**Dependencies**: None

**Next Step**: Step 3 (CreateOrder Lambda needs this role)

---

### Step 3️⃣: CreateOrder Lambda Function

**Deliverable**: `templates/lambda-create-order.yaml` + `lambda/create-order/index.js`

**Function Properties**:
```
Runtime: Node.js 20.x
Handler: index.handler
Memory: 256 MB
Timeout: 30 seconds
Environment Variables:
  - ORDERS_TABLE_NAME: (from DynamoDB stack output)
  - LOG_LEVEL: INFO
Role: (from IAM stack)
Logging: CloudWatch (auto)
```

**Function Logic**:
1. Parse request body (order data)
2. Validate required fields (CustomerID, Items, Address)
3. Generate OrderID (UUID or timestamp-based)
4. Write to DynamoDB Orders table
5. Return OrderID + success status

**Example Input**:
```json
{
  "CustomerID": "CUST-123",
  "Items": [
    {"SKU": "PROD-001", "Quantity": 2, "Price": 29.99}
  ],
  "ShippingAddress": { "street": "123 Main St", "city": "NYC" },
  "BillingAddress": { "street": "123 Main St", "city": "NYC" }
}
```

**Example Output**:
```json
{
  "statusCode": 201,
  "body": {
    "OrderID": "ORD-20261005-abc123",
    "Status": "CREATED",
    "Timestamp": "2026-10-05T12:34:56Z"
  }
}
```

**Dependencies**: Step 1 (DynamoDB table), Step 2 (IAM role)

**Next Step**: Step 4 (API Gateway integrates with this Lambda)

---

### Step 4️⃣: API Gateway HTTP API

**Deliverable**: `templates/api-gateway-http.yaml`

**Endpoints**:
```
POST /orders
  ├─ Request Validation: JSON schema
  ├─ Integration: Lambda (CreateOrder function)
  ├─ Timeout: 29 seconds
  ├─ Response Format: HTTP 201 (success) / 400 (validation error) / 500 (server error)
  └─ CORS: Allow frontend domain
```

**API Response Mapping**:
- **201 Created**: Order successfully created
- **400 Bad Request**: Validation error (missing fields, invalid format)
- **500 Internal Error**: DynamoDB or Lambda failure

**CloudWatch Integration**: Access logs + execution logs enabled

**Dependencies**: Step 3 (CreateOrder Lambda function)

**Next Step**: Step 5 (Frontend calls this API endpoint)

---

### Step 5️⃣: Frontend Order Submission App

**Deliverable**: `frontend/` directory with HTML/React app

**Features**:
```
1. Form Inputs:
   - Customer ID (text input)
   - Product SKU (select dropdown)
   - Quantity (number input)
   - Shipping Address (text inputs)
   - Submit button

2. API Integration:
   - POST to API Gateway endpoint
   - Include order JSON in request body
   - Handle response (OrderID display)

3. User Feedback:
   - Loading spinner during submission
   - Success message with OrderID
   - Error message with details
   - Clear form on success

4. Hosting:
   - Deploy to S3 (static website)
   - Optional: CloudFront for CDN
```

**Technology Stack Options**:
- **Simple**: HTML + Vanilla JavaScript
- **Recommended**: React + Axios/Fetch
- **Full-Stack**: Next.js + TypeScript

**API Call Example**:
```javascript
async function submitOrder(orderData) {
  const response = await fetch(
    'https://api-id.execute-api.us-east-1.amazonaws.com/orders',
    {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify(orderData)
    }
  );
  return response.json();
}
```

**Dependencies**: Step 4 (API Gateway endpoint URL)

---

## 📋 Implementation Checklist (5-Step MVP)

| Step | Component | Status | File | Estimated Time |
| --- | --- | --- | --- | --- |
| 1 | DynamoDB Orders Table | ⏳ | `templates/dynamodb-orders-table.yaml` | 30 mins |
| 2 | Lambda IAM Role | ⏳ | `templates/iam-lambda-role.yaml` | 20 mins |
| 3 | CreateOrder Lambda | ⏳ | `templates/lambda-create-order.yaml` | 45 mins |
| 4 | API Gateway HTTP API | ⏳ | `templates/api-gateway-http.yaml` | 40 mins |
| 5 | Frontend App | ⏳ | `frontend/index.html` or React app | 60 mins |
| — | **Total MVP** | — | — | **~3 hours** |

---

## 🎯 Phase 2: Extended Features (After MVP)

Once Steps 1-5 are complete, these features can be added:

1. **SQS Queue + DLQ** - Async order processing
2. **Lambda Processor** - Handle order fulfillment
3. **EventBridge Pipes** - Route events based on order status
4. **SNS Topics** - Send notifications
5. **CloudWatch Dashboard** - Monitor all metrics

---

## 🔧 Technical Architecture & File Structure

### CloudFormation & Code Organization (5-Step MVP)

```text
cloudformation/
├── template.yaml                      # Root stack - references nested stacks
├── parameters.json                    # Parameter values for current environment
└── stack-config.json                  # Stack metadata and configuration

templates/
├── s3-bucket.yaml                     # ✅ S3 bucket template (Step 0 - Done)
├── dynamodb-orders-table.yaml         # ⏳ DynamoDB (Step 1)
├── iam-lambda-role.yaml               # ⏳ Lambda IAM role (Step 2)
├── lambda-create-order.yaml           # ⏳ CreateOrder Lambda (Step 3)
└── api-gateway-http.yaml              # ⏳ API Gateway HTTP API (Step 4)

lambda/
├── create-order/
│   ├── index.js                       # ⏳ CreateOrder function (Step 3)
│   └── package.json                   # AWS SDK dependencies
└── (future processors, error handlers)

frontend/                              # ⏳ Frontend app (Step 5)
├── index.html                         # HTML form for order submission
├── styles.css                         # Styling
├── app.js                             # JavaScript for API integration
└── package.json                       # (if using React/Node toolchain)

### Phase 2+ Templates (After MVP)
templates-phase2/
├── lambda-processor.yaml              # Order fulfillment
├── lambda-error-handler.yaml          # DLQ error handling
├── sqs-queues.yaml                    # SQS + DLQ
├── eventbridge-pipes.yaml             # Event routing
├── sns-topics.yaml                    # Notifications
└── cloudwatch.yaml                    # Monitoring
```

### Event Flow & Data Model

**Order Event Structure:**

```json
{
  "OrderID": "ORD-20261005-001",
  "Timestamp": "2026-10-05T12:34:56Z",
  "CustomerID": "CUST-123",
  "Status": "NEW",
  "Items": [
    {"SKU": "PROD-001", "Quantity": 2, "Price": 29.99}
  ],
  "ShippingAddress": {...},
  "BillingAddress": {...}
}
```

**DynamoDB Orders Table:**

| Attribute | Type | Key | Notes |
| --- | --- | --- | --- |
| OrderID | String | PK | Unique order identifier |
| Timestamp | String | SK | ISO 8601 timestamp |
| CustomerID | String | GSI-PK | For customer query |
| Status | String | — | NEW, VALIDATED, PROCESSING, COMPLETED, FAILED |
| Items | Map | — | Array of order items |
| ShippingAddress | Map | — | Delivery address |
| BillingAddress | Map | — | Billing address |
| CreatedAt | Number | TTL | Unix timestamp for archival |

### Parameter Naming Convention

```text
{Service}-{Component}-{Environment}

Examples:
- Lambda-Validator-Devl
- DynamoDB-Orders-Prod
- SQS-ProcessingQueue-Stag
- SNS-OrderNotifications-Prod
```

---

## 📚 Documentation

| Document | Purpose | Status |
| --- | --- | --- |
| CLAUDE.md | Project architecture, conventions, development guidelines | ✅ Complete |
| README.md | Project overview, template usage, deployment examples | ✅ Complete |
| PROGRESS.md | This file - work tracking and roadmap | ✅ Complete |
| ARCHITECTURE.md | Detailed architecture, data flow, design decisions | 🔄 Planned |
| DEPLOYMENT.md | Step-by-step deployment guide, troubleshooting | 🔄 Planned |
| TESTING.md | Unit tests, integration tests, deployment validation | 🔄 Planned |

---

## 🔐 Security Considerations

### API Gateway

- [ ] Enable CloudTrail logging
- [ ] Enable WAF for DDoS protection
- [ ] API key or OAuth2 for authentication
- [ ] Request throttling (rate limiting)

### Lambda

- [ ] Restrict IAM policies to least privilege
- [ ] Use VPC endpoints for private DynamoDB access
- [ ] Encrypt environment variables
- [ ] Enable X-Ray tracing for debugging

### DynamoDB

- [ ] Point-in-time recovery enabled
- [ ] Encryption at rest (KMS or default)
- [ ] VPC endpoint for private access
- [ ] No public endpoint exposure

### SQS/SNS

- [ ] Encryption at rest (KMS)
- [ ] Restrict queue/topic policies
- [ ] Encryption in transit (TLS)

### Monitoring

- [ ] Enable CloudTrail for API audit logs
- [ ] CloudWatch alarms for security events
- [ ] VPC Flow Logs for network monitoring

---

## 🎓 Lessons Learned & Best Practices

1. **Nested Stack Pattern**: Templates should be small, focused, and reusable
2. **Parameter Organization**: Group related parameters for better CloudFormation UX
3. **Event-Driven Design**: Use DynamoDB Streams and EventBridge for loose coupling
4. **Error Handling**: Always include DLQ for failed message recovery
5. **Monitoring First**: Add CloudWatch metrics/alarms before production deployment
6. **Infrastructure as Code**: All resources defined in CloudFormation, no manual changes
7. **Environment Parity**: Use parameters to support dev/staging/prod with same templates

---

## 📞 Key Resources

- **AWS Account**: 270453428528
- **AWS Region**: us-east-1
- **Environment**: Development (Sandbox)
- **GitHub Repository**: aws-serverless-order-pipeline
- **CI/CD**: GitHub Actions with AWS OIDC
- **Nested Template Bucket**: subhamay-cfn-templates-bucket-270453428528-us-east-1

---

**Last Status Update**: 2026-10-05 12:00 UTC  
**Next Milestone**: Complete API Gateway & Lambda Validator templates  
**Target Deployment**: CloudFormation stack for Phase 2 (Core Resources)
