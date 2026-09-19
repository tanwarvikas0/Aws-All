# Deploying a Static Website with S3 + CloudFront + ACM + Route 53

This guide documents the full process of hosting a static website (`tanwarvikas.online`) securely using **Amazon S3** (private bucket), **CloudFront** (CDN with Origin Access Control), **AWS Certificate Manager** (SSL), and **Route 53** (DNS).

## Architecture

```
Internet
   |
   v
Route 53
   |
   v
CloudFront
   |
   | OAC (Origin Access Control)
   v
Private S3 Bucket
```

We deliberately **do not** make the S3 bucket public. Instead, CloudFront accesses the bucket privately via OAC, and users only ever talk to CloudFront over HTTPS.

---

## Step 0 — Prepare and Upload Website Files

Package your static site files into a project structure like:

```
tanwarvikas-online/
├── index.html
├── style.css
└── script.js
```

Extract the files locally, then upload all three directly into your S3 bucket (e.g. `tanwarvikas.online`).

> **Note:** Keep the bucket private. Do **not** enable public access or static website hosting mode on the bucket — CloudFront + OAC will handle serving the content securely.

---

## Step 1 — Create an ACM SSL Certificate

1. Open **AWS Console → Certificate Manager (ACM)**.
2. **Important:** Make sure the region (top-right) is set to **US East (N. Virginia) — `us-east-1`**. CloudFront requires certificates to be issued in this region.
3. Click **Request → Request a public certificate**.
4. Enter the fully qualified domain names:
   - `tanwarvikas.online`
   - `*.tanwarvikas.online`
5. Choose **DNS validation**.
6. Click **Request**.

### Validate the Certificate

1. Open the certificate you just created — you'll see the DNS validation records.
2. If your domain uses Route 53, click **Create records in Route 53** to auto-create the validation records.
3. Wait until the certificate status changes to **Issued** (this can take a few minutes).

> ⚠️ Do not proceed to CloudFront setup until the certificate status is **Issued**.

---

## Step 2 — Create the CloudFront Distribution

### 2.1 Open CloudFront
Go to **AWS Console → CloudFront → Create distribution**.

### 2.2 Origin
- Under **Origin domain**, select your S3 bucket. It should look like:
  ```
  tanwarvikas.online.s3.ap-south-1.amazonaws.com
  ```
- ⚠️ **Do not** select the static website endpoint format:
  ```
  tanwarvikas.online.s3-website-....amazonaws.com
  ```
  Always use the standard S3 bucket endpoint.

### 2.3 Origin Access
- Under **Origin access**, select **Origin access control settings (recommended)**.
- Click **Create new OAC**.
  - Name: `tanwarvikas-online-oac`
  - Signing behavior: **Sign requests**
- Click **Create**.

Resulting configuration:
```
Origin           → S3 bucket
Origin access    → Origin access control
OAC name         → tanwarvikas-online-oac
```

### 2.4 Default Cache Behavior
- **Viewer protocol policy:** Redirect HTTP to HTTPS
  ```
  http://tanwarvikas.online  →  https://tanwarvikas.online
  ```
- **Allowed HTTP methods:** `GET, HEAD` (sufficient for a static site)
- Leave other cache settings at their default/recommended values.

### 2.5 Web Application Firewall (WAF)
- For a learning/personal project, select **Do not enable security protections**.
- You can add AWS WAF later if needed.

### 2.6 Settings
- **Price class:** For a personal site, the default recommended price class (or "Use North America, Europe, Asia, Middle East and Africa") is fine.

### 2.7 Alternate Domain Names (CNAMEs)
Add:
- `tanwarvikas.online`
- `www.tanwarvikas.online`

### 2.8 Custom SSL Certificate
- Select the ACM certificate created earlier (`tanwarvikas.online`, covering `*.tanwarvikas.online`).
- ⚠️ Must be the certificate from **us-east-1** — CloudFront will only show certificates from that region.

### 2.9 Default Root Object
Set to:
```
index.html
```
This ensures requests to `https://tanwarvikas.online/` resolve to `index.html` in S3.

### 2.10 Review and Create
Confirm the summary matches:

| Setting | Value |
|---|---|
| Origin | `tanwarvikas.online.s3.ap-south-1.amazonaws.com` |
| Origin access | OAC |
| OAC name | `tanwarvikas-online-oac` |
| Viewer protocol | Redirect HTTP to HTTPS |
| Allowed methods | GET, HEAD |
| Alternate domains | `tanwarvikas.online`, `www.tanwarvikas.online` |
| SSL certificate | `tanwarvikas.online` (us-east-1) |
| Default root object | `index.html` |

Click **Create distribution**.

---

## Step 3 — Update the S3 Bucket Policy

After creating the distribution, AWS will typically show a warning that the S3 bucket policy needs updating.

1. Click **Copy policy** (or navigate to **S3 → Permissions → Bucket policy** manually).
2. Go to **S3 → tanwarvikas.online → Permissions → Bucket policy**.
3. Paste the CloudFront-generated policy. It will look like this:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "AllowCloudFrontServicePrincipal",
      "Effect": "Allow",
      "Principal": {
        "Service": "cloudfront.amazonaws.com"
      },
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::tanwarvikas.online/*",
      "Condition": {
        "StringEquals": {
          "AWS:SourceArn": "arn:aws:cloudfront::ACCOUNT-ID:distribution/DISTRIBUTION-ID"
        }
      }
    }
  ]
}
```

> ⚠️ Do not manually type in `ACCOUNT-ID` or `DISTRIBUTION-ID` — always use the exact policy AWS generates for your specific distribution.

---

## Step 4 — Verify S3 Public Access Settings

Go to **S3 → tanwarvikas.online → Permissions** and confirm:

```
Block all public access = ON
```

This is correct and expected — CloudFront accesses the bucket privately via OAC, so the bucket itself must remain fully private.

---

## Step 5 — Wait for CloudFront Deployment

Go to **CloudFront → Distributions**.

- Status will initially show **Deploying**.
- Wait until it changes to **Enabled** and the deployment completes.
- Note the CloudFront domain assigned, e.g.:
  ```
  dxxxxxxxxxxxxx.cloudfront.net
  ```

---

## Step 6 — Test CloudFront (Before Touching Route 53)

Open the CloudFront domain in a browser, e.g.:
```
https://d1234567890abc.cloudfront.net
```

You should see your site load correctly (e.g. "Vikas Tanwar | Linux & Cloud Engineer").

If you get an **AccessDenied** error:
- Stop here.
- Double-check the bucket policy from Step 3 was applied correctly.
- Do **not** randomly change S3 permissions to "fix" it — verify the OAC + bucket policy setup instead.

---

## Step 7 — Configure Route 53

Once the CloudFront domain works correctly, create DNS records pointing your domain to CloudFront:

```
Route 53
  ├── A record → tanwarvikas.online     → CloudFront distribution
  └── A record → www.tanwarvikas.online → CloudFront distribution
```

(Use an **Alias** A record pointing directly to the CloudFront distribution.)

Once propagated, your site will be live at:
- `https://tanwarvikas.online`
- `https://www.tanwarvikas.online`

---

## Deployment Checklist

- [x] S3 bucket created
- [x] Website files uploaded
- [x] ACM certificate issued (us-east-1)
- [ ] CloudFront distribution created
- [ ] Origin Access Control (OAC) configured
- [ ] S3 bucket policy updated
- [ ] CloudFront distribution deployed (Enabled)
- [ ] Route 53 A records created
- [ ] Final HTTPS test passed

---

## Notes

- Always create/validate ACM certificates in **us-east-1**, regardless of which region your S3 bucket lives in.
- Never enable "Static website hosting" or public access on the S3 bucket when using CloudFront + OAC.
- Test via the raw CloudFront domain before configuring Route 53, so you can isolate DNS issues from CloudFront/S3 configuration issues.
