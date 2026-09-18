# AWS Route 53, CloudFront & ACM Interview Questions & Answers (Beginner → Advanced + Scenarios)

Explained in simple, easy language so preparation feels easy.

---

# 🌐 PART 1: Route 53 (DNS Service)

## 🟢 Beginner Level

### 1. What is Route 53?
**Answer:** Route 53 is AWS's DNS (Domain Name System) service. It translates domain names (like example.com) into IP addresses, so users can reach your website/application. It also handles domain registration and health checking.

### 2. Why is it called "Route 53"?
**Answer:** It's a fun reference to DNS running on **port 53** (the standard port for DNS traffic) — "Route" + "53".

### 3. What is a Hosted Zone in Route 53?
**Answer:** A Hosted Zone is a container that holds all the DNS records for a domain (like example.com). There are two types:
- **Public Hosted Zone:** For routing traffic on the public internet
- **Private Hosted Zone:** For routing traffic within a VPC (internal/private use)

### 4. What are the common DNS record types in Route 53?
**Answer:**
- **A record:** Maps a domain name to an IPv4 address
- **AAAA record:** Maps a domain name to an IPv6 address
- **CNAME record:** Maps a domain name to another domain name (alias)
- **MX record:** Used for email routing
- **TXT record:** Stores text info (often used for domain verification, SPF/DKIM)
- **NS record:** Lists the name servers for the domain

### 5. What is an Alias record, and how is it different from a CNAME?
**Answer:** An Alias record is Route 53's special record type that can point a domain to AWS resources (like an ELB, CloudFront, S3 bucket) — and it works even at the root domain (like example.com), which a normal CNAME cannot do. Also, Alias records don't charge for queries when pointing to AWS resources, and CNAME does have some limitations that Alias avoids.

### 6. What is TTL (Time To Live) in DNS?
**Answer:** TTL tells DNS resolvers how long (in seconds) to cache a DNS record before checking again. A low TTL means changes propagate faster but causes more DNS queries; a high TTL reduces queries but changes take longer to reflect.

---

## 🟡 Intermediate Level

### 7. What routing policies does Route 53 support?
**Answer:**
- **Simple Routing:** One record, no special logic — the basic default
- **Weighted Routing:** Split traffic between resources based on assigned weights (e.g., 70% to one server, 30% to another) — useful for A/B testing or gradual rollouts
- **Latency-based Routing:** Sends users to the region with the lowest latency for them
- **Failover Routing:** Sends traffic to a primary resource, and automatically switches to a backup if the primary is unhealthy
- **Geolocation Routing:** Routes traffic based on the user's geographic location
- **Geoproximity Routing:** Routes traffic based on geographic location, with the ability to shift more/less traffic using a "bias"
- **Multivalue Answer Routing:** Returns multiple healthy IP addresses randomly, works like basic load balancing with health checks

### 8. What is Health Check in Route 53, and how does it help?
**Answer:** A Health Check monitors whether an endpoint (server, application) is healthy or not, using regular requests. If a resource becomes unhealthy, Route 53 can automatically stop routing traffic to it (useful with Failover Routing).

### 9. How does Route 53 domain registration work?
**Answer:** Route 53 lets you register a new domain name directly (like example.com) and automatically creates a hosted zone for it. You can also transfer an existing domain from another registrar into Route 53.

### 10. What is DNSSEC, and does Route 53 support it?
**Answer:** DNSSEC (Domain Name System Security Extensions) adds a layer of authentication to DNS to prevent DNS spoofing/cache poisoning attacks. Yes, Route 53 supports DNSSEC for both domain registration and DNS query signing.

---

## 🔴 Advanced Level

### 11. How would you set up disaster recovery using Route 53?
**Answer:** Use **Failover Routing Policy** with health checks — point traffic to a primary region/resource, and set up a secondary (backup) resource in another region. If the health check on the primary fails, Route 53 automatically routes traffic to the secondary.

### 12. What's the difference between Public and Private Hosted Zones, and when would you use a Private one?
**Answer:** A Public Hosted Zone resolves DNS queries from the internet, while a Private Hosted Zone only resolves queries from within specified VPCs. Private Hosted Zones are used for internal microservices, internal tools, or when you don't want internal domain names exposed to the public internet.

### 13. How does Route 53 achieve high availability globally?
**Answer:** Route 53 uses a global network of DNS servers (Anycast network) spread across the world, so DNS queries are automatically answered by the nearest/available server, giving high availability and low latency by design.

### 14. Can you explain how Route 53 Resolver works with Hybrid Cloud setups?
**Answer:** Route 53 Resolver allows DNS resolution between your VPC and on-premises network. Using **Inbound and Outbound Resolver endpoints**, on-premises servers can resolve AWS private DNS names, and AWS resources can resolve on-premises DNS names — useful for hybrid cloud architecture.

---

# 📦 PART 2: CloudFront (CDN Service)

## 🟢 Beginner Level

### 15. What is CloudFront?
**Answer:** CloudFront is AWS's CDN (Content Delivery Network) service. It caches your content (images, videos, web pages, APIs) at edge locations around the world, so users get content delivered from a server closer to them — resulting in faster load times.

### 16. What is an Edge Location?
**Answer:** Edge Locations are the locaton where data of our copies stored when user request cloudfront server the request to edge locaiton 

### 17. What is an Origin in CloudFront?
**Answer:** The Origin is the source location where your original content is stored — it could be an S3 bucket, an EC2 server, a Load Balancer, or any custom HTTP server. CloudFront pulls content from the origin and caches it at edge locations.

### 18. What is a Distribution in CloudFront?
**Answer:** A Distribution is the configuration you create in CloudFront that tells it what origin to pull content from, what caching rules to use, which domain to serve, etc. There are two types:
- **Web Distribution:** For websites, APIs, and general content
- **RTMP Distribution:** For streaming media (now deprecated, replaced by other approaches)

### 19. What is caching in CloudFront, and why is it important?
**Answer:** Caching means CloudFront stores a copy of your content at edge locations, so repeated requests don't have to go back to the origin server every time. This reduces load on the origin, speeds up delivery, and reduces costs.

---

## 🟡 Intermediate Level

### 20. What is a Cache Behavior in CloudFront?
**Answer:** Cache Behavior defines rules for how CloudFront handles requests matching a specific URL path pattern — like which origin to use, TTL settings, which HTTP methods are allowed, and whether to forward headers/cookies/query strings.

### 21. How does CloudFront handle cache invalidation?
**Answer:** If you update content at the origin but the old version is still cached at edge locations, you can create a **CloudFront Invalidation** to force CloudFront to fetch fresh content from the origin instead of serving the stale cached version. (Note: invalidations have a cost after a certain free quota, so it's often better to use versioned file names instead.)

### 22. What is Origin Access Control (OAC) / Origin Access Identity (OAI)?
**Answer:** These are used to restrict access to an S3 bucket so that it can only be accessed through CloudFront, not directly via the S3 URL. OAC is the newer, more secure recommended method (OAI is the older, being phased out).

### 23. What are Signed URLs and Signed Cookies in CloudFront?
**Answer:** These are used to restrict access to private content:
- **Signed URL:** Gives access to a single specific file, with an expiry time
- **Signed Cookies:** Gives access to multiple files (like all files in a folder) using one cookie, useful for things like a private video streaming site

### 24. How does CloudFront handle HTTPS and SSL/TLS?
**Answer:** CloudFront supports HTTPS between the viewer (user) and CloudFront, and also between CloudFront and the origin. You can use the default CloudFront certificate or attach your own custom SSL certificate (usually via ACM) for a custom domain.

---

## 🔴 Advanced Level

### 25. What is Lambda@Edge, and how is it useful?
**Answer:** Lambda@Edge lets you run small pieces of code (Lambda functions) at CloudFront edge locations, triggered on events like viewer request, viewer response, origin request, or origin response. It's used for things like custom header manipulation, A/B testing, URL rewrites, and authentication at the edge — closer to the user, reducing latency.

### 26. What is CloudFront Functions, and how is it different from Lambda@Edge?
**Answer:** CloudFront Functions is a lighter-weight option for very simple, high-scale, low-latency operations (like header manipulation, URL redirects) written in JavaScript. Compared to Lambda@Edge:
- CloudFront Functions: faster, cheaper, but limited to simple logic, runs only on viewer request/response
- Lambda@Edge: more powerful, supports more languages and complex logic, can also run on origin request/response, but has higher latency and cost

### 27. How would you set up CloudFront for a multi-origin architecture (e.g., static content from S3, dynamic content from EC2/ALB)?
**Answer:** You'd create multiple **origins** in the distribution (one for S3, one for ALB), and then set up **Cache Behaviors** based on path patterns — like `/images/*` and `/static/*` going to S3, and `/api/*` going to the ALB. Each behavior has its own caching and forwarding rules.

### 28. How does CloudFront improve security against DDoS attacks?
**Answer:** CloudFront integrates with **AWS Shield** (basic DDoS protection is automatic and free) and can be combined with **AWS WAF (Web Application Firewall)** to block malicious requests, rate-limit traffic, and filter based on IP/geography/request patterns — all happening at the edge, before traffic even reaches your origin.

---

# 🔒 PART 3: ACM (AWS Certificate Manager)

## 🟢 Beginner Level

### 29. What is ACM?
**Answer:** ACM (AWS Certificate Manager) is a service that lets you easily create, manage, and deploy SSL/TLS certificates for use with AWS services (like CloudFront, ELB, API Gateway), enabling HTTPS for your applications.

### 30. Is ACM free?
**Answer:** Yes — public SSL/TLS certificates issued and managed through ACM for use with supported AWS services are free. You only pay for the underlying AWS resources you use (like CloudFront, Load Balancer).

### 31. How does ACM validate domain ownership?
**Answer:** ACM validates domain ownership in two ways:
- **DNS Validation:** You add a special CNAME record to your DNS (Route 53 can do this automatically) — this is the recommended and faster method
- **Email Validation:** ACM sends a validation email to addresses associated with the domain (like admin@, webmaster@), and you approve it by clicking a link

---

## 🟡 Intermediate Level

### 32. Can ACM certificates be used with EC2 instances directly?
**Answer:** No — ACM certificates cannot be installed directly on an EC2 instance's web server (like Apache/Nginx). They can only be used with integrated AWS services like CloudFront, Elastic Load Balancer (ALB/NLB/CLB), and API Gateway. If you need a certificate directly on EC2, you'd need a third-party certificate (like Let's Encrypt) installed manually.

### 33. Does ACM auto-renew certificates?
**Answer:** Yes — for certificates that are validated via DNS validation and actively used with a supported AWS service, ACM automatically renews them before they expire, so you don't need to manually manage renewal (as long as the DNS validation records stay in place).

### 34. Why do you need to request an ACM certificate in the us-east-1 region for CloudFront specifically?
**Answer:** CloudFront is a global service, but it specifically requires SSL/TLS certificates to be requested in the **US East (N. Virginia) — us-east-1** region, regardless of where your other resources are. This is a well-known "gotcha" — if the certificate is created in another region, CloudFront won't be able to use it.

---

## 🔴 Advanced Level

### 35. Can you import your own (third-party) certificate into ACM?
**Answer:** Yes — ACM supports importing certificates issued by external Certificate Authorities (CAs), like from Let's Encrypt or DigiCert. However, imported certificates are **not auto-renewed** by ACM — you have to manually renew and re-import them before they expire.

### 36. What is ACM Private CA, and how is it different from regular ACM?
**Answer:** ACM Private CA lets you create your own private Certificate Authority to issue private certificates for internal use (like internal microservices, IoT devices, VPNs) that don't need to be trusted publicly on the internet. Regular ACM only issues publicly trusted certificates for public-facing use cases. ACM Private CA is a separate, paid service.

---

# 🧩 PART 4: Scenario-Based Questions (Route 53 + CloudFront + ACM Together)

### Scenario 1: You want your website (example.com) to load fast for users worldwide, with HTTPS enabled
**Question:** How would you design this using Route 53, CloudFront, and ACM together?
**Answer:**
1. Request a public SSL certificate in **ACM** (in **us-east-1**, since CloudFront requires it) for example.com, validated via DNS
2. Create a **CloudFront Distribution** with your origin (S3/ALB/EC2), attach the ACM certificate for the custom domain
3. In **Route 53**, create an **Alias record** pointing example.com to the CloudFront distribution
4. This way, users worldwide are served content from the nearest CloudFront edge location, over HTTPS

### Scenario 2: Your website went down in the primary region, and you need automatic failover to a backup region
**Question:** How would you set this up?
**Answer:**
- Set up **Health Checks** in Route 53 for both the primary and backup resources/endpoints
- Configure **Failover Routing Policy**: primary record marked as "Primary," backup record marked as "Secondary"
- If the health check detects the primary is unhealthy, Route 53 automatically starts routing traffic to the secondary — no manual action needed

### Scenario 3: You updated an image on your website, but users are still seeing the old cached version
**Question:** How would you fix this in CloudFront?
**Answer:**
- Option 1: Create a **CloudFront Invalidation** for that specific file path (quick fix, but has cost after free quota and takes a few minutes)
- Option 2 (better long-term practice): Use **versioned file names** (like image-v2.jpg instead of image.jpg) so each update is automatically a "new" file, and old cached versions naturally expire without needing invalidation

### Scenario 4: You have a private video streaming platform, and only paying subscribers should be able to access videos
**Question:** How would you restrict access using CloudFront?
**Answer:**
- Keep videos in a private S3 bucket, restrict direct access using **Origin Access Control (OAC)** so only CloudFront can read from it
- Generate **Signed Cookies** for logged-in/paying users, giving them time-limited access to multiple video files
- Non-subscribers or expired-cookie users will get access denied when trying to reach the content

### Scenario 5: You want to reduce latency for users hitting your API through CloudFront, with some custom logic (like header checks) at the edge
**Question:** Would you use Lambda@Edge or CloudFront Functions, and why?
**Answer:**
- If the logic is simple (like checking/modifying a header, simple redirect) → use **CloudFront Functions** — faster, cheaper, runs at every edge location with very low latency
- If the logic is more complex (like calling another service, heavier processing) → use **Lambda@Edge**, accepting the slightly higher latency and cost for more powerful capability

### Scenario 6: Your company is migrating a domain from GoDaddy to AWS and wants full DNS management on AWS
**Question:** How would you do this migration?
**Answer:**
1. Create a **Hosted Zone** in Route 53 for the domain
2. Recreate all existing DNS records (A, CNAME, MX, TXT, etc.) from GoDaddy into the new Route 53 hosted zone
3. Verify everything resolves correctly by testing (without switching nameservers yet)
4. Once records are confirmed working, update the **nameservers** at GoDaddy's registrar settings to point to Route 53's nameservers
5. Wait for DNS propagation (based on old TTL values) before considering the migration complete

### Scenario 7: An internal microservice needs to resolve another internal service by a custom domain name, but this should never be reachable from the public internet
**Question:** How would you design DNS for this?
**Answer:**
- Create a **Private Hosted Zone** in Route 53, associated with the specific VPC(s)
- Add internal DNS records (like `service.internal.company.com`) pointing to the internal resource's private IP or internal load balancer
- Since it's a private hosted zone, these records are only resolvable from within the associated VPC — never exposed publicly

---

## 💡 Quick Tips for Interview
- Always mention **why** a certain approach is used, not just what it is — interviewers love reasoning
- Remember the classic "gotcha" facts: ACM certificate for CloudFront must be in **us-east-1**; Alias records don't have the limitations of CNAME
- If you don't know something exactly, explain your general approach/thought process — that matters a lot too

---

*Good luck for your interview! 🚀*
