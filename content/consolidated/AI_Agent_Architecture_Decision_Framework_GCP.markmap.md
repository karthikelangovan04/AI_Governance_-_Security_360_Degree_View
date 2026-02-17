---
title: AI Agent Architecture Decision Framework - GCP Security Governance
markmap:
  colorFreezeLevel: 2
---

# AI Agent Architecture Decision Framework: GCP Security Governance & Services

## Framework Overview

- **Purpose**: Guide architecture decisions for AI agents and MCP servers on GCP
- **Dimensions**: Use case scenarios × Architecture types × Security governance × Cost/ROI
- **Scope**: Production-grade agentic AI with complete security governance

## Architecture Type Definitions

- **Custom Build**
  - Build agent framework/orchestration from scratch
  - Use Vertex AI APIs directly (no ADK framework)
  - Build custom agent loop, tool orchestration, state management
  - Manage all infrastructure (Cloud Run, GKE, Compute Engine)
  - Full control but maximum development effort
- **Framework (ADK-based)**
  - Use **ADK (Agent Development Kit)** framework
  - ADK provides agent patterns, callbacks, Tool Context, plugins
  - You deploy and manage infrastructure (Cloud Run, Agent Engine)
  - Faster development than custom, but you manage ops
  - Good balance of speed and control
- **Hosted Solution (Agent Builder)**
  - Use **Vertex AI Agent Builder** (managed service)
  - Can still use **ADK** to develop agents (ADK agents deploy to Agent Engine)
  - Google manages infrastructure (Agent Engine runtime)
  - Fastest deployment, minimal ops overhead
  - Less infrastructure control, but managed by Google

## Use Case Scenario 1: Internal Productivity Tool (<50 users)

### Custom Build

- **What it means**
  - Build agent framework/orchestration from scratch
  - Use Vertex AI APIs directly (no ADK framework)
  - Custom agent loop, tool orchestration, state management
  - Build your own patterns (callbacks, tool context, etc.)
- **GCP Services**
  - **Vertex AI** (direct API calls to models)
  - **Cloud Run** (custom agent runtime)
  - **Cloud API Registry** (MCP discovery) or custom MCP clients
  - **Secret Manager** (credentials)
  - **Cloud Logging** (audit)
  - **IAM** (RBAC)
- **Security Governance**
  - Custom security patterns (you build them)
  - Service account or agent identity
  - IAM roles: minimal permissions
  - Cloud Logging for audit trail
  - Secret Manager for secrets
  - VPC Service Controls (optional for small team)
- **Cost Estimate**
  - Vertex AI: $0.10-0.50 per 1K requests
  - Cloud Run: $0.40 per million requests + compute
  - Secret Manager: $0.06 per secret version/month
  - Cloud Logging: $0.50 per GB ingested
  - **Monthly**: ~$200-500 for <50 users
- **ROI & Business Outcomes**
  - **Pros**
    - Full control over architecture
    - Custom security policies
    - Learning opportunity for team
    - No framework constraints
    - No vendor lock-in
  - **Cons**
    - High development time (3-6 months)
    - Build everything from scratch (agent loop, tool handling, etc.)
    - Ongoing maintenance burden
    - Requires deep GCP expertise
    - Slower time-to-value
- **Recommendation**: ⚠ Learning opportunity — Consider only if team needs GCP expertise

### Framework (ADK-based)

- **What it means**
  - Use **ADK (Agent Development Kit)** framework
  - ADK provides agent patterns, callbacks, Tool Context, plugins
  - You deploy and manage infrastructure (Cloud Run, Agent Engine)
  - Faster than custom, but you handle ops
- **GCP Services**
  - **ADK (Agent Development Kit)** — framework for building agents
  - **Vertex AI** (models) or **Vertex AI Agent Engine** (if deploying to Agent Engine)
  - **Cloud API Registry** (MCP tools)
  - **Cloud Run** (deployment) or **Agent Engine** (you manage)
  - **Apigee** (optional gateway)
  - **IAM** + **Secret Manager**
  - **Cloud Logging** + **Cloud Trace**
- **Security Governance**
  - ADK built-in safety (Tool Context, callbacks, Before Tool Callback)
  - Cloud API Registry IAM (cloudapiregistry.viewer, mcp.toolUser)
  - Agent identity (if using Agent Engine) or service account
  - Cloud Logging for audit
  - You configure security (not fully managed)
- **Cost Estimate**
  - ADK: Included (no extra cost)
  - Vertex AI: $0.10-0.50 per 1K requests
  - Cloud API Registry: Free tier + usage
  - Cloud Run: $0.40 per million requests + compute
  - Apigee: $0.15-0.30 per 1K API calls (if used)
  - **Monthly**: ~$150-300 for <50 users
- **ROI & Business Outcomes**
  - **Pros**
    - Faster development (1-2 months) vs custom
    - ADK provides built-in patterns and security
    - GCP best practices via ADK
    - Good balance of control vs speed
    - You manage infrastructure (flexibility)
  - **Cons**
    - Framework constraints (ADK patterns)
    - Still requires GCP knowledge
    - You handle infrastructure ops
    - Some customization limits (within ADK)
- **Recommendation**: 💡 Consider — Good balance for small teams

### Hosted Solution

- **What it means**
  - Use **Vertex AI Agent Builder** (managed service)
  - Can use **ADK** to develop agents (ADK agents deploy to Agent Engine)
  - Google manages infrastructure (Agent Engine runtime)
  - You focus on agent logic, Google handles ops
- **GCP Services**
  - **Vertex AI Agent Builder** (hosted Agent Engine runtime)
  - **ADK** (optional - use to build agents that run on Agent Engine)
  - **Google Cloud MCP servers** (hosted MCP)
  - **Cloud API Registry** (can use with Agent Builder)
  - **Identity Platform** (user auth)
  - **Cloud Logging** (managed)
- **Security Governance**
  - Managed security by Google
  - Built-in compliance (SOC2, ISO27001)
  - Agent identity supported (if using Agent Engine)
  - ADK safety features (if using ADK)
  - Automatic updates
  - Google-managed IAM
- **Cost Estimate**
  - Agent Builder/Agent Engine: Pay-per-use pricing
  - ADK: Included (no extra cost)
  - Hosted MCP: Included or minimal cost
  - Identity Platform: $0.0055 per MAU
  - **Monthly**: ~$100-200 for <50 users
- **ROI & Business Outcomes**
  - **Pros**
    - Fastest deployment (days to weeks)
    - Can use ADK for faster development
    - Minimal maintenance (Google manages infrastructure)
    - Built-in security & compliance
    - Focus on business logic
    - Lowest upfront cost
  - **Cons**
    - Less infrastructure customization
    - Vendor dependency
    - Potential cost scaling issues
    - Limited control over runtime infrastructure
- **Recommendation**: ✓ Recommended — Best fit for small internal teams

## Use Case Scenario 2: Consumer-Facing AI Assistant (Unique Domain Knowledge)

### Custom Build

- **GCP Services**
  - **Vertex AI Agent Engine** (agent identity)
  - **Custom MCP servers** on **Cloud Run**
  - **Apigee** (API gateway, rate limits)
  - **Cloud API Registry** (MCP catalog)
  - **BigQuery** (domain knowledge)
  - **Cloud Storage** (documents)
  - **VPC Service Controls** (data protection)
  - **Model Armor** (content safety)
  - **Cloud Logging** + **Cloud Trace**
- **Security Governance**
  - Agent identity per agent
  - VPC-SC perimeter for data
  - Model Armor for prompt injection
  - Apigee API Products + allowed_tools
  - Custom domain knowledge encryption (CMEK)
  - Cloud Logging for all interactions
- **Cost Estimate**
  - Vertex AI: $0.10-0.50 per 1K requests
  - Cloud Run: $0.40 per million + compute
  - Apigee: $0.15-0.30 per 1K calls
  - BigQuery: $5 per TB queried
  - VPC-SC: Included
  - Model Armor: Usage-based
  - **Monthly**: $5K-15K for moderate traffic
- **ROI & Business Outcomes**
  - **Pros**
    - Complete control over domain knowledge
    - Custom security for consumer data
    - Optimized for unique requirements
    - Best performance potential
    - Competitive differentiation
  - **Cons**
    - High development cost ($200K-500K)
    - Long development time (6-12 months)
    - Requires expert team
    - Ongoing maintenance
- **Recommendation**: ✓ Best fit — Maximum control for unique domain

### Framework

- **GCP Services**
  - **ADK** on **Vertex AI**
  - **Cloud API Registry** (MCP tools)
  - **Custom MCP servers** on **Cloud Run**
  - **Apigee** (gateway)
  - **BigQuery** (domain data)
  - **Model Armor** (safety)
  - **IAM** + **Secret Manager**
- **Security Governance**
  - ADK safety + custom MCP servers
  - Apigee API Products for tool access
  - Cloud API Registry for discovery
  - Model Armor integration
  - Custom domain knowledge in BigQuery
- **Cost Estimate**
  - ADK: Included with Vertex AI
  - Cloud Run: $0.40 per million requests
  - Apigee: $0.15-0.30 per 1K calls
  - BigQuery: $5 per TB queried
  - **Monthly**: $3K-10K for moderate traffic
- **ROI & Business Outcomes**
  - **Pros**
    - Faster than custom (3-6 months)
    - Good security patterns
    - Flexible for domain knowledge
    - Lower cost than custom
  - **Cons**
    - Framework limitations
    - Still requires customization
    - Some vendor dependency
- **Recommendation**: ✓ Good option — Balance of speed and control

### Hosted Solution

- **GCP Services**
  - **Vertex AI Agent Builder** (hosted)
  - **Google Cloud MCP servers** (hosted)
  - **BigQuery** (domain data via MCP)
  - **Identity Platform** (consumer auth)
- **Security Governance**
  - Google-managed security
  - Limited custom domain integration
  - Standard compliance
  - Managed updates
- **Cost Estimate**
  - Agent Builder: Pay-per-use
  - Hosted MCP: Included
  - BigQuery: $5 per TB queried
  - **Monthly**: $2K-5K for moderate traffic
- **ROI & Business Outcomes**
  - **Pros**
    - Fastest to market (weeks)
    - Low upfront cost
    - Managed security
    - Focus on domain data
  - **Cons**
    - Limited unique domain integration
    - Vendor dependency for core features
    - May not support all domain needs
    - Less competitive differentiation
- **Recommendation**: ⚠ Vendor dependent — Evaluate if domain needs are met

## Use Case Scenario 3: Rapid Prototype (2-4 weeks)

### Custom Build

- **GCP Services**
  - **Cloud Run** (minimal setup)
  - **Vertex AI** (direct API calls)
  - **Secret Manager** (basic)
- **Security Governance**
  - Minimal security (prototype)
  - Service account auth
  - Basic logging
- **Cost Estimate**
  - Cloud Run: $0.40 per million requests
  - Vertex AI: $0.10-0.50 per 1K requests
  - **Monthly**: $50-200 for prototype
- **ROI & Business Outcomes**
  - **Pros**
    - Full control
    - Learning experience
  - **Cons**
    - Very risky timeline
    - Likely incomplete
    - Security gaps
    - Not production-ready
- **Recommendation**: ⚠ Risky timeline — Not recommended for 2-4 weeks

### Framework

- **GCP Services**
  - **ADK** on **Vertex AI**
  - **Cloud API Registry** (quick MCP setup)
  - **Cloud Run** (deployment)
  - **Cloud Logging** (basic)
- **Security Governance**
  - ADK default security
  - Cloud API Registry quick setup
  - Basic IAM
- **Cost Estimate**
  - ADK: Included
  - Cloud Run: $0.40 per million requests
  - **Monthly**: $100-300 for prototype
- **ROI & Business Outcomes**
  - **Pros**
    - Faster than custom
    - Some security built-in
    - Reasonable for demo
  - **Cons**
    - Tight timeline
    - May need shortcuts
    - Limited customization
- **Recommendation**: ⚠ Tight timeline — Possible but challenging

### Hosted Solution

- **GCP Services**
  - **Vertex AI Agent Builder** (hosted)
  - **Google Cloud MCP servers** (hosted)
  - **Identity Platform** (quick auth setup)
- **Security Governance**
  - Managed security
  - Quick setup
  - Demo-ready
- **Cost Estimate**
  - Agent Builder: Pay-per-use
  - Hosted MCP: Included
  - **Monthly**: $50-150 for prototype
- **ROI & Business Outcomes**
  - **Pros**
    - Fastest path (days to 1 week)
    - Minimal setup
    - Professional demo
    - Low risk
    - Can iterate quickly
  - **Cons**
    - Limited customization
    - May need migration later
- **Recommendation**: ✓ Ideal — Best for rapid prototypes

## Use Case Scenario 4: Enterprise Solution (HIPAA, SOC2 Compliance)

### Custom Build

- **GCP Services**
  - **Vertex AI Agent Engine** (agent identity)
  - **VPC Service Controls** (data perimeter)
  - **Assured Workloads** (compliance)
  - **Cloud KMS** (CMEK for encryption)
  - **Sensitive Data Protection** (DLP API)
  - **Cloud Audit Logs** (compliance audit)
  - **Apigee** (API gateway, compliance)
  - **Cloud Run** (MCP servers)
  - **Cloud API Registry** (governed MCP)
  - **Secret Manager** (encrypted secrets)
  - **Cloud Logging** + **Cloud Trace**
- **Security Governance**
  - VPC-SC service perimeter
  - Assured Workloads for HIPAA/SOC2
  - CMEK for all data
  - DLP for PII/PHI detection
  - Cloud Audit Logs for compliance
  - Agent identity with PAB
  - Apigee API Products + allowed_tools
  - Complete audit trail
  - Data residency controls
- **Cost Estimate**
  - Vertex AI: $0.10-0.50 per 1K requests
  - VPC-SC: Included
  - Assured Workloads: $0.10 per vCPU/month
  - Cloud KMS: $1 per key/month + operations
  - DLP API: $3 per GB scanned
  - Apigee: $0.15-0.30 per 1K calls
  - Cloud Run: $0.40 per million + compute
  - **Monthly**: $10K-50K+ for enterprise scale
- **ROI & Business Outcomes**
  - **Pros**
    - Maximum control for compliance
    - Custom compliance workflows
    - Full auditability
    - Meets strictest requirements
    - No vendor compliance gaps
  - **Cons**
    - Very high cost ($500K-2M+)
    - Long development (12-18 months)
    - Requires compliance experts
    - Ongoing compliance maintenance
    - Complex architecture
- **Recommendation**: ✓ Maximum control — Best for strictest compliance needs

### Framework

- **GCP Services**
  - **ADK** on **Vertex AI**
  - **VPC Service Controls** (perimeter)
  - **Assured Workloads** (compliance)
  - **Cloud KMS** (CMEK)
  - **Sensitive Data Protection** (DLP)
  - **Apigee** (gateway)
  - **Cloud API Registry** (MCP)
  - **Cloud Audit Logs**
- **Security Governance**
  - ADK + compliance services
  - VPC-SC integration
  - Assured Workloads setup
  - DLP integration
  - Framework compliance patterns
- **Cost Estimate**
  - ADK: Included
  - VPC-SC: Included
  - Assured Workloads: $0.10 per vCPU/month
  - Cloud KMS: $1 per key/month
  - DLP: $3 per GB scanned
  - Apigee: $0.15-0.30 per 1K calls
  - **Monthly**: $8K-40K for enterprise scale
- **ROI & Business Outcomes**
  - **Pros**
    - Faster than custom (6-9 months)
    - Framework compliance patterns
    - Lower cost than custom
    - Good security foundation
  - **Cons**
    - Requires careful implementation
    - Framework constraints
    - May need custom compliance logic
    - Still significant cost
- **Recommendation**: ✓ With care — Good if framework supports compliance needs

### Hosted Solution

- **GCP Services**
  - **Vertex AI Agent Builder** (hosted)
  - **Assured Workloads** (if supported)
  - **Google Cloud MCP servers** (hosted)
  - **Identity Platform** (enterprise auth)
- **Security Governance**
  - Google-managed compliance
  - Standard certifications (SOC2, ISO27001)
  - Limited HIPAA support
  - Managed security
- **Cost Estimate**
  - Agent Builder: Pay-per-use
  - Assured Workloads: $0.10 per vCPU/month
  - Hosted MCP: Included
  - **Monthly**: $5K-20K for enterprise scale
- **ROI & Business Outcomes**
  - **Pros**
    - Fastest deployment (weeks to months)
    - Google compliance certifications
    - Lower upfront cost
    - Managed updates
  - **Cons**
    - Vendor dependent for compliance
    - May not meet all requirements
    - Limited customization
    - Compliance gaps possible
- **Recommendation**: ⚠ Vendor dependent — Verify compliance requirements met

## Use Case Scenario 5: Research/Experimental Project (Uncertain Requirements)

### Custom Build

- **GCP Services**
  - **Vertex AI** (experimentation)
  - **Cloud Run** (flexible runtime)
  - **Cloud API Registry** (try different MCPs)
  - **Cloud Logging** (experiment tracking)
- **Security Governance**
  - Minimal security (research)
  - Basic IAM
  - Experiment tracking
- **Cost Estimate**
  - Vertex AI: $0.10-0.50 per 1K requests
  - Cloud Run: $0.40 per million requests
  - **Monthly**: $100-500 variable
- **ROI & Business Outcomes**
  - **Pros**
    - Maximum flexibility
    - No constraints
    - Learning opportunity
  - **Cons**
    - Beware of trade-offs
    - May over-engineer
    - Time-consuming
    - Technical debt risk
- **Recommendation**: ⚠ Beware of trade-offs — Only if research needs justify

### Framework

- **GCP Services**
  - **ADK** on **Vertex AI**
  - **Cloud API Registry** (experiment with MCPs)
  - **Cloud Run** (deployment)
  - **Vertex AI Pipelines** (experiment tracking)
- **Security Governance**
  - ADK flexibility
  - Easy MCP experimentation
  - Experiment versioning
- **Cost Estimate**
  - ADK: Included
  - Cloud Run: $0.40 per million requests
  - Vertex AI Pipelines: Usage-based
  - **Monthly**: $50-300 variable
- **ROI & Business Outcomes**
  - **Pros**
    - Flexible for experiments
    - Easy to pivot
    - Good for research
    - Lower commitment
  - **Cons**
    - Framework constraints
    - May limit some experiments
- **Recommendation**: ✓ Flexible — Best for research projects

### Hosted Solution

- **GCP Services**
  - **Vertex AI Agent Builder** (hosted)
  - **Google Cloud MCP servers** (hosted)
  - **Vertex AI Studio** (experimentation)
- **Security Governance**
  - Managed security
  - Quick experiments
  - Easy to start/stop
- **Cost Estimate**
  - Agent Builder: Pay-per-use
  - Vertex AI Studio: Free tier + usage
  - **Monthly**: $0-200 variable
- **ROI & Business Outcomes**
  - **Pros**
    - Lowest commitment
    - Easy to start/stop
    - Fast experimentation
    - No infrastructure management
    - Can pivot easily
  - **Cons**
    - Limited customization
    - Vendor dependency
- **Recommendation**: ✓ Low commitment — Ideal for uncertain requirements

## Use Case Scenario 6: High-Volume Production (Millions of Interactions)

### Custom Build

- **GCP Services**
  - **Vertex AI Agent Engine** (scalable agents)
  - **Cloud Run** (auto-scaling MCP servers)
  - **Cloud Load Balancing** (traffic distribution)
  - **Cloud CDN** (caching)
  - **Cloud Spanner** (high-scale data)
  - **Pub/Sub** (async processing)
  - **Cloud Monitoring** (scalability monitoring)
  - **Apigee** (rate limits, quotas)
  - **VPC Service Controls** (at scale)
  - **Cloud Logging** (high-volume logs)
- **Security Governance**
  - Optimized security for scale
  - Efficient IAM patterns
  - Scalable audit logging
  - Performance-optimized VPC-SC
  - Apigee quotas and rate limits
- **Cost Estimate**
  - Vertex AI: $0.10-0.50 per 1K requests × millions
  - Cloud Run: $0.40 per million + compute × scale
  - Cloud Load Balancing: $0.025 per GB + $0.008 per rule
  - Cloud Spanner: $0.90 per node/hour
  - Pub/Sub: $40 per million messages
  - Apigee: $0.15-0.30 per 1K calls × millions
  - **Monthly**: $50K-500K+ for millions of interactions
- **ROI & Business Outcomes**
  - **Pros**
    - Best performance potential
    - Optimized for scale
    - Custom optimizations
    - Cost optimization possible
    - Maximum control
  - **Cons**
    - Very high development cost ($1M-5M+)
    - Long development time (12-24 months)
    - Requires scale experts
    - Complex architecture
    - High operational overhead
- **Recommendation**: ✓ Performance — Best for maximum scale optimization

### Framework

- **GCP Services**
  - **ADK** on **Vertex AI** (scalable)
  - **Cloud Run** (auto-scaling)
  - **Cloud API Registry** (MCP at scale)
  - **Apigee** (gateway)
  - **Cloud Monitoring** (scale monitoring)
- **Security Governance**
  - ADK scalability
  - Framework scale patterns
  - Apigee for rate limits
- **Cost Estimate**
  - ADK: Included
  - Cloud Run: $0.40 per million + compute
  - Apigee: $0.15-0.30 per 1K calls
  - **Monthly**: $30K-200K for millions of interactions
- **ROI & Business Outcomes**
  - **Pros**
    - Faster than custom (6-12 months)
    - Framework scale patterns
    - Lower cost than custom
    - Good performance
  - **Cons**
    - Beware of limitations
    - Framework constraints at scale
    - May hit limits
    - Still significant cost
- **Recommendation**: ⚠ Beware of limitations — Evaluate framework scale limits

### Hosted Solution

- **GCP Services**
  - **Vertex AI Agent Builder** (hosted, auto-scaling)
  - **Google Cloud MCP servers** (hosted, scalable)
  - **Cloud Load Balancing** (if needed)
- **Security Governance**
  - Google-managed scale
  - Auto-scaling security
  - Managed compliance
- **Cost Estimate**
  - Agent Builder: Pay-per-use × millions
  - Hosted MCP: Usage-based × millions
  - **Monthly**: $20K-200K+ for millions (can scale high)
- **ROI & Business Outcomes**
  - **Pros**
    - Fastest to scale (weeks)
    - Auto-scaling built-in
    - Managed infrastructure
    - Focus on business logic
  - **Cons**
    - Beware of costs at scale
    - Vendor pricing at volume
    - Less cost control
    - Potential cost surprises
- **Recommendation**: ⚠ Beware of costs — Monitor costs closely at scale

## Additional Dimensions

### Security Governance Dimensions

- **Authentication & Identity**
  - Agent identity (Vertex AI Agent Engine)
  - Service accounts (IAM)
  - Identity Platform (users)
  - Workload Identity Federation
- **Authorization & Access Control**
  - IAM roles and policies
  - Principal Access Boundary (PAB)
  - Apigee API Products + allowed_tools
  - Cloud API Registry IAM
- **Network & Perimeter Security**
  - VPC Service Controls (VPC-SC)
  - VPC networks (private ingress)
  - Cloud Armor (DDoS protection)
  - Private Google Access
- **Data Protection**
  - Cloud KMS (CMEK)
  - Sensitive Data Protection (DLP)
  - Encryption at rest/transit
  - Data residency controls
- **Compliance & Audit**
  - Assured Workloads (HIPAA, SOC2)
  - Cloud Audit Logs
  - Cloud Logging (comprehensive)
  - Compliance reporting
- **Content Safety**
  - Model Armor (prompt injection protection)
  - Gemini content filters
  - ADK safety callbacks
  - Tool Context validation

### Cost Optimization Strategies

- **Right-Sizing**
  - Start with hosted, migrate if needed
  - Use Cloud Run auto-scaling
  - Monitor and optimize
- **Reserved Capacity**
  - Committed use discounts (CUD)
  - Sustained use discounts
  - Preemptible instances (if applicable)
- **Architecture Optimization**
  - Caching (Cloud CDN)
  - Async processing (Pub/Sub)
  - Efficient data storage
  - Batch processing where possible
- **Cost Monitoring**
  - Cloud Billing budgets
  - Cost alerts
  - Regular cost reviews
  - Cost allocation tags

### ROI Factors

- **Time-to-Market**
  - Hosted: Fastest (days to weeks)
  - Framework: Medium (months)
  - Custom: Slowest (6-24 months)
- **Development Cost**
  - Hosted: Lowest upfront
  - Framework: Medium
  - Custom: Highest
- **Operational Cost**
  - Hosted: Managed (predictable)
  - Framework: Some management
  - Custom: High operational overhead
- **Flexibility & Control**
  - Custom: Maximum
  - Framework: Good balance
  - Hosted: Limited
- **Scalability**
  - All can scale, but:
  - Custom: Best optimization potential
  - Framework: Good patterns
  - Hosted: Auto-scaling built-in

### Business Outcomes by Scenario

- **Internal Productivity (<50 users)**
  - Outcome: Team efficiency, cost savings
  - KPI: Time saved, adoption rate
  - Best: Hosted solution (fast, low cost)
- **Consumer-Facing (Unique Domain)**
  - Outcome: Competitive differentiation, revenue
  - KPI: User engagement, conversion
  - Best: Custom or Framework (control + speed)
- **Rapid Prototype (2-4 weeks)**
  - Outcome: Proof of concept, stakeholder buy-in
  - KPI: Demo success, feedback quality
  - Best: Hosted solution (fastest)
- **Enterprise Compliance (HIPAA, SOC2)**
  - Outcome: Compliance, risk mitigation
  - KPI: Audit success, compliance score
  - Best: Custom or Framework (with care)
- **Research/Experimental**
  - Outcome: Innovation, learning
  - KPI: Experiments run, insights gained
  - Best: Hosted or Framework (flexibility)
- **High-Volume Production (Millions)**
  - Outcome: Scale, revenue at volume
  - KPI: Throughput, latency, cost per transaction
  - Best: Custom (optimization) or Hosted (if costs acceptable)

## Decision Matrix Summary

| Scenario | Custom Build | Framework | Hosted Solution |
|----------|-------------|-----------|-----------------|
| **Internal Productivity (<50)** | ⚠ Learning | 💡 Consider | ✓ Recommended |
| **Consumer-Facing (Domain)** | ✓ Best fit | ✓ Good option | ⚠ Vendor dependent |
| **Rapid Prototype (2-4 weeks)** | ⚠ Risky timeline | ⚠ Tight timeline | ✓ Ideal |
| **Enterprise Compliance** | ✓ Maximum control | ✓ With care | ⚠ Vendor dependent |
| **Research/Experimental** | ⚠ Beware trade-offs | ✓ Flexible | ✓ Low commitment |
| **High-Volume Production** | ✓ Performance | ⚠ Beware limitations | ⚠ Beware costs |

## Key Clarification: ADK vs Infrastructure Management

### Important Distinction

- **ADK (Agent Development Kit)** is a framework for building agents
  - Provides patterns: callbacks, Tool Context, plugins, safety features
  - Can be used in both Framework and Hosted approaches
  - Not required for Custom Build (you build your own patterns)
- **Infrastructure Management** is what differentiates the three types:
  - **Custom Build**: You build framework + manage infrastructure
  - **Framework (ADK)**: You use ADK framework + manage infrastructure
  - **Hosted**: You use ADK (optional) + Google manages infrastructure

### ADK Usage Across Architecture Types

| Architecture Type | Uses ADK? | Infrastructure Management |
|-------------------|-----------|---------------------------|
| **Custom Build** | ❌ No (build your own patterns) | ✅ You manage (Cloud Run, etc.) |
| **Framework** | ✅ Yes (ADK framework) | ✅ You manage (Cloud Run, Agent Engine) |
| **Hosted** | ✅ Yes (optional - ADK agents on Agent Engine) | ❌ Google manages (Agent Builder) |

### Example Workflows

- **Custom Build**: Write agent loop from scratch → Use Vertex AI APIs directly → Deploy to Cloud Run → Manage scaling/monitoring
- **Framework (ADK)**: Use ADK to build agent → Deploy ADK agent to Cloud Run or Agent Engine → You manage infrastructure
- **Hosted**: Use ADK to build agent → Deploy to Vertex AI Agent Builder → Google manages Agent Engine runtime

## GCP Service Recommendations by Architecture Type

### Custom Build Services

- **Core**
  - Vertex AI Agent Engine (agent identity)
  - Cloud Run (MCP servers)
  - Cloud API Registry (MCP catalog)
- **Security**
  - VPC Service Controls
  - Cloud KMS (CMEK)
  - Secret Manager
  - Sensitive Data Protection (DLP)
- **Governance**
  - Cloud Audit Logs
  - Cloud Logging + Cloud Trace
  - Assured Workloads (if compliance needed)
- **Scale**
  - Cloud Load Balancing
  - Cloud CDN
  - Pub/Sub
  - Cloud Spanner (if needed)

### Framework Services

- **Core**
  - ADK on Vertex AI
  - Cloud API Registry
  - Cloud Run
- **Security**
  - IAM (roles, PAB)
  - Secret Manager
  - Model Armor (optional)
- **Governance**
  - Cloud Logging
  - Cloud Trace
  - ADK safety callbacks
- **Scale**
  - Cloud Run auto-scaling
  - Apigee (if gateway needed)

### Hosted Solution Services

- **Core**
  - Vertex AI Agent Builder (managed Agent Engine)
  - ADK (optional - use to build agents)
  - Google Cloud MCP servers (hosted)
  - Cloud API Registry (can use with Agent Builder)
  - Identity Platform (if user auth needed)
- **Security**
  - Google-managed security
  - Agent identity (if using Agent Engine)
  - ADK safety features (if using ADK)
  - Built-in compliance
- **Governance**
  - Managed logging
  - Google compliance certifications
  - ADK callbacks/tool context (if using ADK)
- **Scale**
  - Auto-scaling built-in
  - Google-managed infrastructure
  - No infrastructure ops needed

## Implementation Roadmap

### Phase 1: Assessment

- Evaluate use case scenario
- Assess security requirements
- Estimate scale and cost
- Choose architecture type

### Phase 2: Security Setup

- Set up authentication (Agent identity or Service accounts)
- Configure IAM roles (least privilege)
- Set up VPC-SC (if needed)
- Configure Secret Manager
- Set up Cloud Logging

### Phase 3: Development

- Develop agents (Custom/Framework/Hosted)
- Set up MCP servers (if custom)
- Configure Cloud API Registry (if using)
- Set up Apigee (if gateway needed)
- Implement security controls

### Phase 4: Testing & Compliance

- Security testing
- Performance testing
- Compliance validation (if needed)
- Cost optimization
- Documentation

### Phase 5: Production

- Deploy to production
- Monitor (Cloud Monitoring)
- Audit (Cloud Logging)
- Optimize costs
- Iterate based on feedback

## References

- **Security Governance**: [Security_Compliance_Agents_MCP_GCP.markmap.md](Security_Compliance_Agents_MCP_GCP.markmap.md)
- **GCP Service Mapping**: [09_Hyperscaler_Cloud_Service_Mapping.md](09_Hyperscaler_Cloud_Service_Mapping.md)
- **Authentication Guide**: [02_Authentication.md](02_Authentication.md)
- **Executive Summary**: [00_Executive_Summary.md](00_Executive_Summary.md)
