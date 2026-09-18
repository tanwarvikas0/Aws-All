# AWS EC2 Interview Questions — Beginner to Advanced

---

## 🟢 BEGINNER LEVEL

### 1. What is EC2?
**Answer:** EC2 (Elastic Compute Cloud) is an AWS service that gives you a virtual computer (called an **instance**) in the cloud. You can use it to run applications, host websites, or do any computing task — just like a normal computer, but you rent it from AWS instead of buying hardware.

### 2. What does "Elastic" mean in EC2?
**Answer:** "Elastic" means you can easily increase or decrease the number/size of instances based on your need. If traffic increases, you scale up; if traffic decreases, you scale down. You pay only for what you use.

### 3. What is an AMI?
**Answer:** AMI (Amazon Machine Image) is like a template/blueprint used to create an EC2 instance. It contains the OS, application server, and any pre-installed software. Think of it like a "ready-made recipe" to launch a server.

### 4. What are the different instance types in EC2?
**Answer:** EC2 instances are grouped by purpose:
- **General Purpose (T, M series):** balanced CPU/memory — good for web servers
- **Compute Optimized (C series):** high CPU — good for gaming servers, batch processing
- **Memory Optimized (R, X series):** high RAM — good for databases
- **Storage Optimized (I, D series):** high disk speed — good for big data
- **Accelerated Computing (P, G series):** has GPU — good for ML/AI, graphics

### 5. What is a Key Pair in EC2?
**Answer:** A key pair is used to securely log in to your instance. It has two parts:
- **Public key** — stored by AWS in the instance
- **Private key (.pem file)** — downloaded by you

You use the private key to SSH (log in) into the instance. If you lose it, you can't log in the normal way.

### 6. What is a Security Group?
**Answer:** A Security Group is like a **virtual firewall** for your instance. It controls what traffic can come in (inbound) and go out (outbound). Example: allow port 22 (SSH) only from your IP, allow port 80 (HTTP) from everyone.

### 7. What is the difference between Stop, Terminate, and Reboot?
**Answer:**
- **Stop** — instance shuts down, but disk (EBS) data stays. You can start it again later.
- **Terminate** — instance is permanently deleted. Data is lost (unless EBS is set to persist).
- **Reboot** — just restarts the OS, like a normal computer restart.

### 8. What is EBS?
**Answer:** EBS (Elastic Block Store) is the **hard disk** attached to your EC2 instance. It stores your OS, files, and data. It stays even if you stop the instance (unlike instance store).

### 9. What is the difference between EBS and Instance Store?
**Answer:**
- **EBS** — persistent (data stays even after stop/restart), can be detached/attached
- **Instance Store** — temporary storage physically attached to the host; data is **lost** if instance stops or terminates

### 10. What is a Public IP vs Elastic IP?
**Answer:**
- **Public IP** — automatically assigned, but changes every time you stop/start the instance
- **Elastic IP** — a fixed public IP that you own and can attach to any instance; it doesn't change

---

## 🟡 INTERMEDIATE LEVEL

### 11. What is the difference between EC2 and Lambda?
**Answer:** EC2 is a full virtual server — you manage the OS, scaling, and it runs 24/7 (you pay even when idle). Lambda is **serverless** — you just upload code, AWS runs it only when triggered, and you pay only for execution time. Use EC2 for long-running apps, Lambda for short event-driven tasks.

### 12. What is Auto Scaling in EC2?
**Answer:** Auto Scaling automatically adds or removes EC2 instances based on demand (like CPU usage or traffic). If load increases, it launches new instances; if load decreases, it terminates extra ones. This saves cost and maintains performance.

### 13. What is a Load Balancer and why do we use it with EC2?
**Answer:** A Load Balancer (like ALB/NLB) distributes incoming traffic across multiple EC2 instances so no single server gets overloaded. It also improves availability — if one instance fails, traffic is routed to healthy ones.

### 14. What is the difference between ALB, NLB, and CLB?
**Answer:**
- **ALB (Application Load Balancer)** — works at Layer 7 (HTTP/HTTPS), good for routing based on URL path
- **NLB (Network Load Balancer)** — works at Layer 4 (TCP/UDP), used for very high performance/low latency
- **CLB (Classic Load Balancer)** — old generation, rarely used now

### 15. What are EC2 Placement Groups?
**Answer:** Placement groups control how instances are physically placed on AWS hardware:
- **Cluster** — instances close together (low latency, high performance — for HPC apps)
- **Spread** — instances on different hardware (high availability, reduces failure risk)
- **Partition** — groups of instances spread across partitions (used for big data apps like Hadoop)

### 16. What is the difference between On-Demand, Reserved, Spot, and Dedicated Instances?
**Answer:**
- **On-Demand** — pay per hour/second, no commitment, most expensive
- **Reserved** — commit for 1 or 3 years, get big discount
- **Spot** — use AWS's spare capacity at very low price, but AWS can take it back anytime
- **Dedicated Host/Instance** — physical server just for you, used for compliance needs

### 17. What is an EC2 Instance Metadata?
**Answer:** It's information about your running instance (like instance ID, IP, region) that you can access from inside the instance using a special internal URL: `http://169.254.169.254/latest/meta-data/`. Useful for scripts that need to know their own instance details.

### 18. What is the use of User Data in EC2?
**Answer:** User Data is a script you provide at launch time that runs automatically when the instance starts for the first time. It's commonly used to install software, update packages, or configure the server automatically (no manual login needed).

### 19. How do you connect to an EC2 instance?
**Answer:**
- **Linux instance** — use SSH with the private key: `ssh -i key.pem ec2-user@public-ip`
- **Windows instance** — use RDP (Remote Desktop) with a password decrypted using the private key

### 20. What is an Elastic Network Interface (ENI)?
**Answer:** ENI is a virtual network card that can be attached to an EC2 instance. It has its own IP, MAC address, and security groups. You can detach it from one instance and attach it to another — useful for failover setups.

---

## 🔴 ADVANCED LEVEL

### 21. How does EC2 Auto Scaling decide when to scale?
**Answer:** It uses **scaling policies** based on CloudWatch metrics (like CPU > 70%). Types of policies:
- **Target Tracking** — keep a metric at a target value (e.g., CPU at 50%)
- **Step Scaling** — add/remove instances in steps based on how far the metric is from threshold
- **Scheduled Scaling** — scale at specific times (e.g., more instances during business hours)

### 22. What is the difference between Vertical and Horizontal Scaling in EC2?
**Answer:**
- **Vertical Scaling** — increase the size of a single instance (e.g., t2.micro → t2.large). Has a limit and needs downtime.
- **Horizontal Scaling** — add more instances of the same size. This is what Auto Scaling does, and it has no real limit.

### 23. What happens during an EC2 instance's boot process from a networking perspective?
**Answer:** When launched, AWS assigns the instance a private IP (from the VPC subnet) and optionally a public IP. The instance gets its network config via DHCP from AWS. Security groups and NACLs (Network ACLs) are checked for every packet before traffic is allowed in/out.

### 24. What is the difference between Security Groups and Network ACLs?
**Answer:**
| Feature | Security Group | Network ACL |
|---|---|---|
| Level | Instance level | Subnet level |
| Rules | Only "Allow" rules | Allow AND Deny rules |
| State | Stateful (return traffic auto-allowed) | Stateless (must allow both directions) |
| Evaluation | All rules checked | Rules checked in order (by number) |

### 25. How does EC2 achieve high availability across a region?
**Answer:** By deploying instances across multiple **Availability Zones (AZs)** within a region, combined with a Load Balancer and Auto Scaling Group. If one AZ fails, traffic automatically shifts to instances in the healthy AZ.

### 26. What is EC2 Hibernate?
**Answer:** Hibernate saves the RAM (memory) content to the EBS root volume when you stop the instance. When you start it again, it resumes exactly where it left off (like waking a laptop from sleep), instead of a full OS boot — much faster.

### 27. How do you secure an EC2 instance? (Best practices)
**Answer:**
- Use IAM roles instead of hardcoding access keys
- Restrict Security Group rules to specific IPs/ports (avoid 0.0.0.0/0 for SSH)
- Use Systems Manager Session Manager instead of SSH/key pairs where possible
- Keep OS and software patched regularly
- Enable EBS encryption
- Use VPC with private subnets for sensitive instances

### 28. What is an IAM Role and why is it used with EC2 instead of Access Keys?
**Answer:** An IAM Role is attached directly to the EC2 instance and gives it temporary, auto-rotating permissions to access other AWS services (like S3, DynamoDB) without storing any secret keys on the instance. This is more secure than hardcoding access keys in code, which can be leaked.

### 29. What is the difference between EC2 and ECS/EKS?
**Answer:** EC2 gives you raw virtual machines that you manage fully. ECS (Elastic Container Service) and EKS (Elastic Kubernetes Service) run **containers** on top of EC2 (or Fargate) and handle orchestration, scaling, and scheduling of containers for you — good when you're using Docker-based microservices.

### 30. How does EC2 pricing work with Savings Plans vs Reserved Instances?
**Answer:**
- **Reserved Instances** — commit to a specific instance type/region for 1-3 years for discount
- **Savings Plans** — commit to a certain $ amount of usage per hour for 1-3 years; more flexible because it applies across instance types/families automatically

---

## 🎯 SCENARIO-BASED QUESTIONS

### Scenario 1: Your website is slow during high traffic hours
**Q: How would you fix this using EC2?**
**Answer:** Set up an **Auto Scaling Group** behind an **Application Load Balancer**. Configure a target-tracking policy (e.g., scale out when CPU > 60%). This way, when traffic increases, new instances launch automatically and the load balancer spreads traffic evenly. When traffic drops, extra instances are removed to save cost.

### Scenario 2: You accidentally terminated an EC2 instance. How do you recover data?
**Answer:** If the **EBS volume** had "Delete on Termination" set to **No**, the volume survives even after termination. You can attach that volume to a new instance and recover the data. This is why it's a best practice to set critical volumes to NOT delete on termination, and take regular **snapshots**.

### Scenario 3: You need to run a database on EC2 that needs high I/O performance
**Answer:** Choose an EBS volume type like **io2/io1 (Provisioned IOPS SSD)** for high, consistent performance, and pick a **Memory Optimized instance (R-series)** since databases need lots of RAM for caching. Also consider Multi-AZ setup for high availability.

### Scenario 4: Your company wants to save cost on non-critical batch jobs that can tolerate interruption
**Answer:** Use **Spot Instances**. They're up to 90% cheaper than On-Demand. Since batch jobs aren't time-critical and can restart, it's fine if AWS reclaims the instance occasionally. Combine with Auto Scaling and a Spot Fleet for reliability.

### Scenario 5: You need instances to always run in the same physical rack for ultra-low latency (like HPC workloads)
**Answer:** Use a **Cluster Placement Group**. This places instances physically close together within a single AZ, giving very low network latency and high throughput between them — ideal for tightly-coupled workloads like HPC or big data processing.

### Scenario 6: Your EC2 instance can't connect to the internet even though it has a public IP
**Answer:** Check these in order:
1. Is the instance in a **public subnet** with a route to an **Internet Gateway**?
2. Does the **Security Group** allow outbound traffic?
3. Does the **Network ACL** allow both inbound and outbound traffic (since NACLs are stateless)?
4. Does the instance actually have a public IP or Elastic IP assigned?

### Scenario 7: You want zero downtime while upgrading your app on EC2
**Answer:** Use a **Blue-Green Deployment**: launch a new set of instances (Green) with the updated app behind the same Load Balancer, test them, then switch traffic from old (Blue) to new (Green) instances. If something breaks, you can switch back instantly.

### Scenario 8: Sensitive application must not be accessible from the internet at all
**Answer:** Launch the EC2 instance in a **private subnet** (no route to Internet Gateway, no public IP). To manage it, use a **Bastion Host** in a public subnet, or better, use **AWS Systems Manager Session Manager**, which doesn't need any inbound ports open at all.

### Scenario 9: You need to move an EC2 instance from one AZ to another
**Answer:** You can't move an instance directly between AZs. Instead:
1. Create an **AMI** (snapshot/image) of the current instance
2. Launch a new instance from that AMI in the target AZ
3. Update DNS/Load Balancer to point to the new instance
4. Terminate the old one once confirmed working

### Scenario 10: Interviewer asks — "How would you design a highly available 3-tier web application using EC2?"
**Answer:**
- **Web tier:** EC2 instances in an Auto Scaling Group across multiple AZs, behind an Application Load Balancer
- **App tier:** Separate Auto Scaling Group in private subnets, only reachable from the web tier
- **Database tier:** RDS Multi-AZ (or EC2 with replication) in private subnets, not publicly accessible
- Use Security Groups to allow only necessary tier-to-tier communication
- Store static assets in S3 + CloudFront for performance

---

## 💡 Quick Tips for the Interview
- Always explain the **"why"** behind an answer, not just the definition — interviewers love reasoning.
- For scenario questions, structure your answer as: **Problem → Solution → Why it works**.
- If you don't know something fully, explain the closest concept you do know — partial correct reasoning is better than silence.
- Mention **cost** and **security** wherever relevant — AWS interviewers love hearing you think about both.

Good luck with your interview! 🚀
