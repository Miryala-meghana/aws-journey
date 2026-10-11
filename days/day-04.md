# Day 4 - VPC (Virtual Private Cloud)

## Topics covered
- What is VPC?
- Why is VPC needed
- Components of VPC and how they interact with each other

## What I learned

### 1. What is VPC?
A **VPC (Virtual Private Cloud)** is your own logically isolated, private network within AWS — like your own private data center network, but virtual. You control its IP address range, subnets, routing, and connectivity to the internet or other networks. Every resource you launch (EC2, RDS, etc.) sits inside a VPC.

### 2. Why is VPC needed
- **Isolation** — your resources are logically separated from every other AWS customer's resources, even though the underlying hardware is shared.
- **Control over networking** — define your own IP ranges, split resources into public-facing and private-only subnets, control exactly what can talk to what.
- **Security** — place sensitive resources (e.g., databases) in private subnets with no direct internet access, only reachable from inside the VPC.
- **Connectivity options** — connect your VPC to on-premises data centers (VPN, Direct Connect) or peer with other VPCs, enabling hybrid/multi-network architectures.
- Without a VPC concept, every AWS customer's instances would effectively sit on one flat shared network — not secure or manageable at scale.

### 3. Components of VPC and how they interact
- **VPC** — the overall container; defined by a CIDR block (e.g., `10.0.0.0/16`), which is the total range of private IP addresses available inside it.
- **Subnets** — subdivisions of the VPC's CIDR range, each tied to a single Availability Zone. Two types:
  - **Public subnet** — has a route to the internet (via an Internet Gateway); resources here can have public IPs (e.g., a web server).
  - **Private subnet** — no direct route to the internet; used for backend resources (e.g., databases, app servers) that shouldn't be directly reachable.
- **Internet Gateway (IGW)** — attached to the VPC; allows resources in public subnets to send/receive traffic to/from the internet.
- **Route Table** — a set of rules (routes) that determine where network traffic from a subnet is directed. Each subnet is associated with one route table.
  - A public subnet's route table has a route pointing `0.0.0.0/0` → Internet Gateway.
  - A private subnet's route table typically has no direct internet route (or routes outbound traffic via a NAT Gateway instead).
- **NAT Gateway** — sits in a public subnet; lets instances in a **private** subnet initiate outbound connections to the internet (e.g., to download updates) without being directly reachable from the internet themselves.
- **Security Groups** — virtual firewalls attached at the instance level; stateful (if you allow inbound, the matching outbound response is automatically allowed).
- **Network ACLs (NACLs)** — firewalls at the subnet level; stateless (inbound and outbound rules must both be defined explicitly); act as an additional layer of defense on top of security groups.

**How they interact (request flow example — a user hitting a web app):**
1. Internet traffic reaches the **Internet Gateway**.
2. The **Route Table** for the public subnet directs that traffic to the right subnet/instance.
3. The instance's **Security Group** checks if the specific port/protocol is allowed inbound.
4. The subnet's **NACL** also checks the traffic against its allow/deny rules.
5. If the web server (public subnet) needs to talk to a database (private subnet), that traffic stays inside the VPC — it's routed directly via the VPC's internal routing, never touching the Internet Gateway.
6. If the private-subnet database needs outbound internet access (e.g., for patching), its route table sends that traffic to the **NAT Gateway**, which sits in the public subnet and forwards it to the Internet Gateway — so the response can come back, but nothing from the internet can initiate a connection directly to the database.

## Interview Q&A quick revision
- **Q: What is a VPC in one line?** A logically isolated, private virtual network within AWS where you control IP ranges, subnets, and routing.
- **Q: Difference between a public and private subnet?** A public subnet has a route to an Internet Gateway (resources can be internet-reachable); a private subnet has no direct internet route.
- **Q: What does an Internet Gateway do?** Allows resources in public subnets to communicate with the internet.
- **Q: What's the purpose of a NAT Gateway?** Lets instances in a private subnet initiate outbound internet connections (e.g., updates) without being directly reachable from the internet.
- **Q: Security Groups vs Network ACLs?** Security groups are stateful and apply at the instance level; NACLs are stateless and apply at the subnet level, acting as an additional layer of defense.
- **Q: Why put a database in a private subnet instead of a public one?** To prevent direct internet access to it — it should only be reachable from within the VPC (e.g., from an app server), reducing attack surface.
