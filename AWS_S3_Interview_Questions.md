# AWS S3 Interview Questions — Beginner to Advanced (with Scenarios)

---

## 🟢 BEGINNER LEVEL

### 1. What is Amazon S3?
Amazon S3 (Simple Storage Service) is an object storage service that offers scalability, data availability, security, and performance. Data is stored as **objects** inside **buckets**.

### 2. What is a bucket in S3?
A container for storing objects. Bucket names must be **globally unique** across all AWS accounts.

### 3. What is an object in S3?
The fundamental entity stored in S3. It consists of:
- **Key** (file name/path)
- **Value** (the actual data/bytes)
- **Version ID** (if versioning enabled)
- **Metadata**
- **Access control info**

### 4. What is the maximum size of an object in S3?
5TB. For objects larger than 100MB, AWS recommends using **multipart upload**.

### 5. Is S3 a block storage or object storage service?
Object storage — not suitable for OS boot volumes or file-system-level operations (use EBS/EFS for that).

### 6. What are the different storage classes in S3?
- S3 Standard
- S3 Intelligent-Tiering
- S3 Standard-IA (Infrequent Access)
- S3 One Zone-IA
- S3 Glacier Instant Retrieval
- S3 Glacier Flexible Retrieval
- S3 Glacier Deep Archive
- S3 Express One Zone (high-performance, single-AZ)

### 7. What is the difference between S3 Standard and S3-IA?
S3 Standard is for frequently accessed data with higher storage cost. S3-IA has lower storage cost but a retrieval fee, meant for infrequently accessed data still needing millisecond access.

### 8. Is S3 a regional or global service?
Buckets are created in a specific **region**, but the S3 service (like the bucket namespace and console) has global aspects — bucket names are unique globally.

### 9. What is a pre-signed URL?
A URL generated with temporary credentials that grants time-limited access to a private S3 object without requiring AWS credentials from the requester.

### 10. What is the default durability and availability of S3 Standard?
- Durability: 99.999999999% (11 nines)
- Availability: 99.99%

### 11. Can two buckets have the same name?
No. Bucket names are globally unique across all AWS accounts and regions.

### 12. What protocols can be used to access S3?
REST API (HTTPS), and SDKs (boto3, AWS CLI, Console, SFTP via AWS Transfer Family).

---

## 🟡 INTERMEDIATE LEVEL

### 13. What is S3 Versioning?
A feature that keeps multiple variants of an object in the same bucket. Once enabled, it cannot be disabled — only **suspended**.

### 14. What happens when you delete an object in a versioned bucket?
A **delete marker** is added as the new "current" version. The actual object isn't deleted — you can restore it by removing the delete marker.

### 15. What is a Lifecycle Policy in S3?
Rules to automatically transition objects between storage classes or expire (delete) them after a defined period — e.g., move to Glacier after 90 days, delete after 365 days.

### 16. What is Cross-Region Replication (CRR) vs Same-Region Replication (SRR)?
- **CRR**: replicates objects to a bucket in a different region (compliance, latency, DR).
- **SRR**: replicates within the same region (log aggregation, prod/test sync).
- Both require versioning enabled on source and destination buckets.

### 17. How do you secure an S3 bucket?
- Bucket Policies (resource-based, JSON)
- IAM Policies (identity-based)
- ACLs (legacy, object/bucket-level)
- Block Public Access settings
- Encryption (SSE-S3, SSE-KMS, SSE-C, client-side)
- VPC Endpoints (Gateway endpoint for private access)

### 18. What is the difference between a Bucket Policy and an IAM Policy?
Bucket Policy is attached to the S3 bucket and controls access from any principal (including cross-account). IAM Policy is attached to a user/role/group and controls what that identity can do across AWS services.

### 19. What are the S3 encryption options?
- **SSE-S3**: S3-managed keys (AES-256)
- **SSE-KMS**: AWS KMS-managed keys, adds audit trail via CloudTrail
- **SSE-C**: Customer-provided keys (you manage the key, AWS does the encryption)
- **Client-Side Encryption**: You encrypt before upload

### 20. What is Multipart Upload?
Splits a large object into parts, uploads them in parallel, and S3 reassembles them. Recommended for objects > 100MB, required for objects > 5GB.

### 21. What is S3 Transfer Acceleration?
Uses CloudFront's globally distributed edge locations to speed up uploads to S3 over long distances.

### 22. What's the difference between S3 and EBS?
| S3 | EBS |
|---|---|
| Object storage | Block storage |
| Accessed over network/API | Attached to a single EC2 instance |
| Virtually unlimited storage | Fixed provisioned size |
| Great for static files, backups, data lakes | Great for OS/DB volumes |

### 23. What is S3 Event Notification?
Triggers actions (Lambda, SQS, SNS) when events occur in a bucket — e.g., `s3:ObjectCreated:*`, `s3:ObjectRemoved:*`.

### 24. How is data consistency handled in S3?
S3 provides **strong read-after-write consistency** for all operations (PUTs and DELETEs) since December 2020 — including for overwrite PUTs and DELETEs, across all regions.

### 25. What is the difference between Same Object PUT and Object Overwrite behavior with Versioning off vs on?
Without versioning, an overwrite PUT replaces the object entirely (old data is gone). With versioning on, both versions are retained.

---

## 🔴 ADVANCED LEVEL

### 26. Explain S3 Object Lock.
Provides WORM (Write Once Read Many) protection to prevent deletion/overwriting of objects for a fixed period or indefinitely. Two modes:
- **Governance mode**: users with special permissions can override
- **Compliance mode**: no one, including root, can override until retention expires

### 27. What is S3 Access Points?
Named network endpoints attached to a bucket that simplify managing access for large shared datasets — each access point has its own policy and can be restricted to a VPC.

### 28. What is the difference between Gateway and Interface VPC Endpoints for S3?
- **Gateway Endpoint**: free, routes S3 traffic privately within the region, added to route table.
- **Interface Endpoint (PrivateLink)**: uses an ENI with a private IP, works cross-region/on-prem via Direct Connect/VPN, has hourly + data cost.

### 29. How does S3 achieve such high durability (11 nines)?
By automatically storing data redundantly across a minimum of 3 Availability Zones within a region (except One Zone-IA), plus continuous integrity checks and automatic repair of corrupted/lost data.

### 30. What is S3 Intelligent-Tiering and how does it work internally?
Automatically moves objects between access tiers based on changing access patterns, without performance impact or retrieval fees, using monitoring + automation at the object level. Has frequent, infrequent, archive instant, archive, and deep archive access tiers.

### 31. What is the difference between S3 Standard-IA and S3 One Zone-IA?
One Zone-IA stores data in a **single AZ** (lower cost, lower resilience — data lost if AZ is destroyed), whereas Standard-IA stores across multiple AZs.

### 32. Explain how S3 handles eventual vs strong consistency historically, and what changed.
Originally S3 offered eventual consistency for overwrite PUTs/DELETEs (read-after-write only for new object PUTs). Since Dec 2020, AWS introduced **strong consistency** for all operations at no extra cost/performance penalty.

### 33. What's the maximum number of buckets per account, and can it be increased?
Default soft limit is 100 buckets per account; can request a quota increase up to 1,000 via Service Quotas.

### 34. How do you optimize S3 performance for high request rates?
- S3 now automatically scales to support very high request rates (3,500 PUT/COPY/POST/DELETE and 5,500 GET/HEAD per second **per prefix**) — so distributing keys across prefixes horizontally scales throughput further.
- Use multipart upload/parallel GETs for large objects.
- Use CloudFront/Transfer Acceleration for latency reduction.

### 35. What is S3 Batch Operations?
Lets you perform large-scale operations (copy, tagging, ACL updates, Lambda invocation, Object Lock retention) on billions of objects with a single request, using a manifest file.

### 36. How does S3 integrate with AWS Lambda for event-driven architectures?
S3 event notifications (ObjectCreated, ObjectRemoved, etc.) can directly invoke Lambda functions, or route via SNS/SQS for fan-out/decoupling and retry handling.

### 37. What is Requester Pays?
A bucket setting where the requester (not the bucket owner) pays for data transfer and request costs — useful for public datasets.

### 38. Explain S3 Storage Lens.
An analytics feature giving organization-wide visibility into S3 usage and activity trends, with recommendations to optimize cost and apply best practices, across accounts/regions.

### 39. What is the difference between SSE-KMS and SSE-S3 in terms of performance and cost?
SSE-KMS incurs additional API call charges (calls to KMS for every encrypt/decrypt) and can hit KMS request-rate limits at high throughput, whereas SSE-S3 is free and has no such throttling, but offers no per-key access control or CloudTrail-based audit logging of key usage.

### 40. How would you design a highly available, cost-optimized data archival solution using S3?
- Use S3 Standard for active/hot data.
- Lifecycle policy to transition to S3-IA after 30 days, Glacier Flexible Retrieval after 90 days, Deep Archive after 180 days.
- Enable versioning + Object Lock (compliance mode) for regulatory retention.
- Enable Cross-Region Replication for DR.
- Use S3 Inventory + Storage Lens for cost auditing.

---

## 🎯 SCENARIO-BASED QUESTIONS

### Scenario 1: Accidental Deletion
**Q: A team member accidentally deleted critical files from a bucket. How do you recover them, and how would you prevent this in future?**
- If **versioning was enabled**: the delete only added a delete marker — remove the delete marker to restore the object.
- If versioning was **not** enabled, the object is likely unrecoverable unless backed up elsewhere (cross-region replica, backup bucket, AWS Backup).
- Prevention: enable versioning, enable MFA Delete, restrict `s3:DeleteObject` via IAM, use Object Lock for critical data.

### Scenario 2: Public Data Leak
**Q: You discover a bucket containing sensitive customer data is publicly accessible. What immediate and long-term steps do you take?**
- Immediate: Enable "Block Public Access" at the bucket/account level, remove overly permissive bucket policy/ACL, rotate any exposed credentials/secrets.
- Investigate: Check CloudTrail logs for who accessed the data and when.
- Long-term: Enforce Block Public Access at the **account level** (S3 Account-level setting), set up AWS Config rules / Macie to detect sensitive data and public buckets automatically, add SCPs in AWS Organizations to prevent public bucket creation.

### Scenario 3: Cost Optimization
**Q: Your company's S3 bill has grown 5x in 6 months with the same usage pattern. How do you investigate and fix it?**
- Use **S3 Storage Lens** and **Cost Explorer** to identify which buckets/prefixes are driving cost.
- Check for: lack of lifecycle policies (old data still in Standard), excessive versioning without expiration rules on old versions, high request counts (e.g., a Lambda looping and calling GetObject repeatedly), incomplete multipart uploads not cleaned up (they still incur storage cost).
- Fixes: add lifecycle rules to expire old versions/incomplete multipart uploads, move cold data to IA/Glacier, use Intelligent-Tiering for unpredictable access patterns.

### Scenario 4: Large File Upload Failing
**Q: Users report that uploading a 10GB file to S3 keeps failing/timing out. How do you fix this?**
- Use **Multipart Upload** to split the file into parts uploaded in parallel with retry per part instead of the whole file.
- If uploads are from geographically distant users, enable **S3 Transfer Acceleration**.
- Ensure client SDK timeout/retry settings are tuned appropriately.

### Scenario 5: Cross-Account Access
**Q: Team A in Account 1 needs to read objects from a bucket owned by Team B in Account 2, without creating IAM users in Account 2. How do you set this up?**
- Add a **bucket policy** on Account 2's bucket granting `s3:GetObject` to Account 1's IAM role/user ARN as principal.
- Alternatively, set up an **IAM role with a trust policy** allowing Account 1 to assume a role in Account 2 (cross-account role assumption via STS).
- Consider **S3 Access Points** scoped for that specific team's access pattern.

### Scenario 6: Compliance Requirement
**Q: Your company must retain financial records for 7 years and ensure they cannot be deleted or modified, even by admins. How do you implement this in S3?**
- Enable **Versioning**.
- Enable **Object Lock in Compliance Mode** with a 7-year retention period — this ensures even the root user cannot delete/overwrite the objects until retention expires.
- Use a **Legal Hold** if retention duration is uncertain (event-based, no fixed expiry).

### Scenario 7: Disaster Recovery
**Q: How would you design S3 for a disaster recovery strategy where a whole AWS region could go down?**
- Enable **Cross-Region Replication (CRR)** to a bucket in a secondary region.
- Ensure the secondary region infra (app/DB) can also fail over (Route 53 health checks + failover routing).
- Enable versioning on both buckets (a CRR requirement).
- Regularly test failover/restoration procedures.

### Scenario 8: Static Website Hosting
**Q: You need to host a static website using S3 with a custom domain and HTTPS. How do you architect this?**
- Enable **Static Website Hosting** on the bucket, upload index.html/error.html.
- Since S3 website endpoints don't support HTTPS natively, put **CloudFront** in front of the bucket for HTTPS via ACM certificate.
- Use **Route 53** to point the custom domain (alias record) to the CloudFront distribution.
- Use **Origin Access Control (OAC)** so the bucket stays private and is only accessible via CloudFront.

### Scenario 9: Data Lake Architecture
**Q: You're designing a data lake on S3 for a large analytics team using Athena/EMR. What best practices would you apply?**
- Use a logical prefix/partitioning structure (e.g., `s3://bucket/year=2026/month=09/day=19/`) to optimize Athena query performance and cost (less data scanned).
- Store data in columnar formats (Parquet/ORC) for cost/performance efficiency.
- Use S3 Lifecycle policies to tier raw/cold data to Glacier.
- Apply Lake Formation for fine-grained access control if multiple teams access the same data lake.
- Enable S3 Inventory for auditing what data exists.

### Scenario 10: High Throughput Application
**Q: Your application needs to write millions of small objects per second into S3. What issues might you hit and how do you design around them?**
- S3 auto-scales, but request-rate performance scales **per prefix** — so if all keys share a common prefix (e.g., sequential timestamps), you can hit throttling. Randomize/hash prefixes to distribute load.
- Consider batching small objects if write costs (PUT request pricing) become significant, since S3 charges per request.
- Monitor with CloudWatch request metrics and enable S3 Request Metrics on specific prefixes.

---

## 💡 Quick Tips for the Interview
- Always mention **trade-offs** (cost vs durability vs latency) — interviewers look for judgment, not memorized facts.
- For scenario questions, structure your answer as: **Immediate fix → Root cause investigation → Long-term prevention**.
- Be ready to sketch architecture diagrams (S3 + CloudFront + Lambda + DynamoDB is a very common serverless pattern asked about).
- Know the **numbers**: 11 nines durability, 99.99% availability, 5TB max object size, 100MB multipart recommendation threshold, 5GB multipart mandatory threshold.
