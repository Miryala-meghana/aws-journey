# Day 1 - Introduction to AWS

## Topics covered
- What is Cloud?
- Public vs Private Cloud
- Why is public cloud so popular?
- Why AWS?
- Trends of people moving back to private cloud
- Create an AWS account and get started

## What I learned

### 1. What is Cloud?
Cloud computing is the delivery of computing resources — servers, storage, databases, networking, software — over the internet ("on-demand"), instead of owning and maintaining physical hardware yourself. You rent compute/storage from a provider (AWS, Azure, GCP) and pay only for what you use.

**Key characteristics (interview point):**
- On-demand self-service
- Broad network access
- Resource pooling (multi-tenancy)
- Rapid elasticity (scale up/down quickly)
- Measured service (pay-as-you-go)

### 2. Public vs Private Cloud
| | Public Cloud | Private Cloud |
|---|---|---|
| Owned by | Third-party provider (AWS, Azure, GCP) | Organization itself (or dedicated hosted) |
| Infrastructure | Shared (multi-tenant) | Dedicated to one organization |
| Cost model | Pay-as-you-go, no upfront hardware cost | High upfront (CapEx) + maintenance |
| Scalability | Virtually unlimited, elastic | Limited by owned hardware |
| Control/Security | Less direct control, provider manages infra | Full control, often used for strict compliance/data-residency needs |
| Examples | AWS, Azure, GCP | On-prem data center, OpenStack, VMware private cloud |

**Hybrid cloud** = mix of both (e.g., sensitive data on-prem, burst workloads to public cloud).

### 3. Why is public cloud so popular?
- **No CapEx** — no need to buy physical servers; shift from capital expense to operating expense (OpEx).
- **Elasticity** — scale resources up/down based on demand (e.g., handle a traffic spike during a sale).
- **Global reach** — deploy in multiple regions worldwide within minutes.
- **Managed services** — databases, Kubernetes, ML, monitoring, etc. are offered as a service, reducing operational overhead.
- **Faster time-to-market** — spin up infrastructure in minutes instead of weeks of procurement.
- **Reliability** — built-in redundancy, backups, high availability across data centers.

### 4. Why AWS?
- First mover and market leader in cloud (launched 2006) → largest ecosystem, most mature services, biggest community/support.
- Widest range of services (200+) — compute, storage, databases, networking, ML/AI, IoT, serverless, etc.
- Global infrastructure — most regions and availability zones among providers.
- Strong enterprise adoption → more jobs, more learning resources, closely tied to DevOps/cloud-native tooling.
- Pay-as-you-go pricing with a generous free tier for learning.

### 5. Trends of people moving back to private cloud (cloud repatriation)
- **Cost at scale** — for very large, steady/predictable workloads, public cloud can become more expensive long-term than owning hardware.
- **Data sovereignty / compliance** — regulations (finance, healthcare, government) sometimes require data to stay on-prem or in specific jurisdictions.
- **Latency-sensitive workloads** — some workloads need to be physically close to users/devices (edge computing).
- **Vendor lock-in concerns** — heavy reliance on one provider's proprietary services makes migration hard.
- **Predictable/steady-state workloads** — if usage doesn't fluctuate, the elasticity benefit of public cloud matters less, so owning hardware can be cheaper.
- Reality: most companies land on a **hybrid approach** rather than fully exiting public cloud.

### 6. Create an AWS account and get started
Steps covered:
1. Go to aws.amazon.com → "Create an AWS Account"
2. Provide email, password, AWS account name
3. Provide contact information (personal/business)
4. Add a payment method (card required even for free-tier usage — AWS may charge a small temporary verification amount)
5. Identity verification (phone/SMS or call)
6. Select a Support Plan (Basic/Free is enough to start)
7. Sign in to the AWS Management Console using the root user
8. **Best practice (security):** Don't use the root account for daily work — create an IAM user with appropriate permissions, enable MFA on the root account, and lock away root credentials.

## Hands-on / labs
- Created an AWS account
- Logged into the AWS Management Console

## Interview Q&A quick revision
- **Q: Define cloud computing in one line.** On-demand delivery of IT resources over the internet with pay-as-you-go pricing.
- **Q: Public vs private cloud — when would you choose private?** When data residency/compliance, strict security control, or predictable large-scale steady workloads make owning infrastructure cheaper/safer.
- **Q: Why is AWS preferred over other providers?** Market maturity, breadth/depth of services, largest global infrastructure footprint, strongest ecosystem and community support.
- **Q: What is cloud repatriation and why does it happen?** Moving workloads back from public cloud to on-prem/private cloud, usually driven by cost at scale, compliance, or latency requirements.
- **Q: Should you use the AWS root account for everyday tasks?** No — create an IAM user with least-privilege permissions and enable MFA on root; use root only for account-level tasks.
