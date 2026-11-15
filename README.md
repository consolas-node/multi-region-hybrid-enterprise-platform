# multi-region-hybrid-enterprise-platform

### Overview
Multi-Region Hybrid Enterprise Platform is a modular Infrastructure-as-Code(IaC) project designed to architect, deploy and validate a resilient, secure, and automated enterprise cloud environment on AWS. Built entirely with Terraform, this platform bridges simulated on-promises infrastructure with an active-passive multi-region AWS footprint, emphasizing network path control, automated disaster recovery, zero-downtime delivery and policy-driven security governance. 

### Core Architect & Modules
``/01-hybrid-network`` | Hybrid Connectivity & Centralized Inspection

**Hub-and-Spoke Topology** : Connects segmented Workload and Shared Services VPCs through AWS Transit Gateway(TGW) with isolated route tables to enforce east-west and north-south traffic inspection..

**Centralized Egress & IP** : Routes inter-VPC and internet-bound traffic through a dedicated Inspection VPC housing AWS Network Firewall, validating stateful inspection and TGW Appliance Mode to prevent asymmetric routing drops.

**Dynamic BGP Routing & Hybrid DNS**: Simulates an on-premises datacenter using EC2 instances running StrongSwan (IPsec VPN) and FRR (BGP), manipulating AS_PATH prepending and MED attributes for deterministic path selection alongside Route 53 Resolver endpoints and AWS PrivateLink.

``/02-multi-region-ha`` | Multi-Region High Availability & Failover

**Cross-Region Backbone** : Establishes static routing over Inter-Region TGW Peering between ``ap-northeast-2`` and ``ap-northeast-1``.

**Global Data Replication** : Implements Aurora Global Database and DynamoDB Global Tables to benchmark cross-region replication latency, managed planned failovers, and unplanned disaster recovery workflows.

**Automated Traffic Shifting**: Configures Route 53 DNS Failover backed by CloudWatch metric health checks to automatically reroute user traffic upon regional degradation.

``/03-cicd-blue-green`` | Zero-Downtime Delivery & Automated Rollback

**Progressive Deployment Pipeline** : Automates container builds and deployments to Amazon ECS (Fargate) using CodePipeline, CodeBuild, and CodeDeploy (Canary and Linear traffic shifting strategies).

**Lifecycle Validation & Auto-Rollback** : Integrates AppSpec lifecycle hooks (AfterAllowTestTraffic) with AWS Lambda smoke tests on isolated test listeners, paired with CloudWatch Alarms to trigger instantaneous rollbacks upon 5xx error spikes.

``/04-governance-secops`` | Multi-Account Governance & Auto-Remediation

**Preventative Guardrails** : Enforces organizational boundaries across OUs using AWS Organizations Service Control Policies (SCPs) to restrict unauthorized regions and protect audit logging infrastructure.

**Event-Driven Self-Healing** : Combines AWS Config compliance rules, Amazon EventBridge, and AWS Systems Manager (SSM) Automation Runbooks to detect and automatically remediate security drifts (e.g., unrestricted SSH ingress or public S3 access) in real time.

**Centralized Audit & Threat Detection** : Aggregates organization-wide CloudTrail logs and VPC Flow Logs into an immutable, aws:PrincipalOrgID-restricted security archive.

### Repository Structure

```text
multi-region-hybrid-enterprise-platform/
├── 01-hybrid-network/       # TGW, Network Firewall, StrongSwan/FRR BGP, Route 53 Resolver
├── 02-multi-region-ha/      # Inter-Region TGW Peering, Aurora/DynamoDB Global, Route 53 Failover
├── 03-cicd-blue-green/      # ECS Fargate, CodeDeploy Blue/Green, AppSpec Hooks, Auto-Rollback
├── 04-governance-secops/    # Organizations SCP, AWS Config, SSM Automation, Central Logging
└── docs/
    └── break-and-fix-logs/  # Fault injection scenarios, packet traces, and troubleshooting notes
```