# Day 3 - EC2 (Elastic Compute Cloud)

## Topics covered
- What is EC2
- Why use EC2
- EC2 instance types
- Regions and availability zones
- AWS console navigation
- Launching an instance
- Connecting to the instance
- Deploying an application

## What I learned

### 1. What is EC2
EC2 (Elastic Compute Cloud) is AWS's core compute service — it lets you rent **virtual servers** (instances) in the cloud instead of buying physical hardware. You choose the OS, CPU/RAM size, storage, and networking, and AWS provisions it for you in minutes. It's the cloud equivalent of a physical machine you'd otherwise rack in a data center.

### 2. Why use EC2
- **No upfront hardware cost** — pay only for compute time used (per-second/hour billing).
- **Elastic** — scale instance count or size up/down based on demand.
- **Full control** — choose OS (Linux/Windows), install any software, configure networking/security.
- **Fast provisioning** — a new server is ready in minutes, not weeks of procurement.
- **Wide variety of configurations** — from tiny free-tier instances to GPU/high-memory machines for specialized workloads.

### 3. EC2 instance types
Instance types are grouped by the kind of workload they're optimized for:
- **General purpose** (e.g., `t2`, `t3`, `m5`) — balanced CPU/memory/network, good default for most apps.
- **Compute optimized** (e.g., `c5`) — high CPU-to-memory ratio, for CPU-heavy workloads (batch processing, gaming servers).
- **Memory optimized** (e.g., `r5`) — large RAM, for in-memory databases/caches.
- **Storage optimized** (e.g., `i3`) — high, fast local disk I/O, for data warehousing/NoSQL databases.
- **Accelerated computing** (e.g., `p3`, `g4`) — GPUs, for ML/graphics workloads.

Naming pattern: `t2.micro` → `t2` = family/generation, `micro` = size (nano, micro, small, medium, large, xlarge, …). The **free tier** typically includes `t2.micro`/`t3.micro`.

### 4. Regions and Availability Zones
- A **Region** is a geographic area (e.g., `us-east-1`, `ap-south-1`) containing multiple, isolated data centers.
- An **Availability Zone (AZ)** is one or more discrete data centers within a region, each with independent power/networking/cooling, but connected with low-latency links to other AZs in the same region.
- Deploying across multiple AZs gives **high availability** — if one AZ fails, others keep serving traffic.
- Choosing a region close to your users reduces latency; some services/pricing also vary by region.

### 5. AWS console navigation
- The **region selector** (top-right) determines which region you're currently operating in — most resources (EC2 instances, VPCs) are region-scoped, so always check this first.
- The **search bar** (top) is the fastest way to jump to any service (e.g., type "EC2").
- The **services menu** groups all AWS services by category (Compute, Storage, Database, etc.).
- **Account menu** (top-right) shows billing, account settings, and org info.

### 6. Launching an instance
Steps covered:
1. EC2 console → **Instances** → **Launch instance**
2. Name the instance
3. Choose an **AMI** (Amazon Machine Image) — the OS/software template (e.g., Amazon Linux, Ubuntu)
4. Choose an **instance type** (e.g., `t2.micro` for free tier)
5. Create or select a **key pair** (for SSH login) — download the `.pem` file, it's shown only once
6. Configure **network settings** — VPC, subnet, and a **security group** (acts as a firewall — define which ports/IPs are allowed, e.g., port 22 for SSH, port 80 for HTTP)
7. Configure storage (root EBS volume size)
8. **Launch instance**

### 7. Connecting to the instance
- **Linux instance via SSH:**
  ```
  chmod 400 my-key.pem
  ssh -i "my-key.pem" ec2-user@<public-ip-or-dns>
  ```
- **EC2 Instance Connect** — browser-based SSH session directly from the AWS console, no local key setup needed (good for quick tests).
- Security group must allow inbound traffic on port 22 (SSH) from your IP for either method to work.

### 8. Deploying an application
Basic flow for a simple app:
1. Connect to the instance (SSH)
2. Install required runtime (e.g., `sudo yum install -y java-17` or `python3`, `node`, etc.)
3. Transfer app code (e.g., `scp` from local machine, or `git clone` if hosted on GitHub)
4. Run the application (e.g., `java -jar app.jar`, or set up as a service for it to persist after disconnecting)
5. Open the required port in the **security group** (e.g., port 8080) so the app is reachable from the browser
6. Access the app via `http://<public-ip>:<port>`

## Hands-on / labs

### 1. Launched a `t3.micro` EC2 instance (Amazon Linux) using the free tier
1. AWS Console → EC2 → **Launch instance**
2. **Name**: `test-servermeg`
3. **AMI**: Amazon Linux 2023 (free tier eligible)
4. **Instance type**: `t3.micro` (free tier eligible)
5. **Key pair**: Create new key pair → name it `test` → key type RSA → format `.pem` → **Create key pair** (downloads as `test.pem`, save it — can't be re-downloaded)
6. **Network settings** → Edit:
   - Auto-assign public IP: Enable
   - Create security group with: SSH (22) from My IP, and Custom TCP (8080) from Anywhere (0.0.0.0/0)
7. Storage: leave default (8 GB gp3)
8. **Launch instance**, wait for status "Running" with a public IPv4 assigned

### 2. Created a key pair and connected via SSH from the terminal
```powershell
cd D:\path\to\downloaded\key
ssh -i "test.pem" ec2-user@<instance-public-ip>
```
If Windows complains the key file permissions are too open:
```powershell
icacls "test.pem" /inheritance:r
icacls "test.pem" /grant:r "$($env:USERNAME):(R)"
```
Type `yes` when prompted about host authenticity — prompt changes to `[ec2-user@ip-... ~]$` once connected.

### 3. Configured a security group to allow SSH (22) and a custom app port
1. EC2 → Instances → select instance → **Security** tab → click the security group
2. **Inbound rules** → **Edit inbound rules**
3. Ensure: SSH (22) from My IP, and Custom TCP (8080, or app's port) from 0.0.0.0/0
4. **Save rules**

### 4. Installed a runtime and deployed a simple test application
Example — a tiny Python web server:
```bash
sudo yum update -y
sudo yum install -y python3

mkdir myapp && cd myapp
cat > app.py << 'EOF'
import http.server, socketserver
PORT = 8080
Handler = http.server.SimpleHTTPRequestHandler
with socketserver.TCPServer(("", PORT), Handler) as httpd:
    print("Serving on port", PORT)
    httpd.serve_forever()
EOF

echo "<h1>Hello from EC2!</h1>" > index.html

# Run in background so it survives disconnecting
nohup python3 app.py > app.log 2>&1 &
```
Accessed via browser at `http://<instance-public-ip>:8080`.

**Cleanup reminder:** stop/terminate the instance (EC2 → Instances → Instance state → Stop/Terminate) when done, to avoid charges even on free tier.

## Interview Q&A quick revision
- **Q: What is EC2 in one line?** AWS's service for renting resizable virtual servers (compute) in the cloud, billed by usage.
- **Q: What is an AMI?** A template (OS + pre-installed software) used to launch EC2 instances from.
- **Q: What's the difference between a region and an availability zone?** A region is a geographic area with multiple data centers; an AZ is one (or more) isolated data center within that region, used for high availability.
- **Q: What is a security group?** A virtual firewall attached to an instance that controls inbound/outbound traffic by port, protocol, and source/destination IP.
- **Q: Why deploy across multiple AZs?** To tolerate the failure of a single data center — traffic can keep flowing via the other AZs.
- **Q: How do you securely connect to a Linux EC2 instance?** Via SSH using the private key (`.pem`) downloaded at launch, with the security group allowing inbound port 22 from your IP.
