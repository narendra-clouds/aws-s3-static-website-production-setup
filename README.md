# 🌐 AWS S3 Static Website Hosting — Production-Grade Setup

[![AWS](https://img.shields.io/badge/AWS-S3%20%2B%20CloudFront-orange?style=for-the-badge&logo=amazon-aws)](https://aws.amazon.com/)
[![Region](https://img.shields.io/badge/Region-ap--south--1%20Mumbai-blue?style=for-the-badge)](https://ap-south-1.console.aws.amazon.com/)
[![HTTPS](https://img.shields.io/badge/HTTPS-Enabled%20via%20ACM-green?style=for-the-badge)](https://aws.amazon.com/cloudfront/)
[![Status](https://img.shields.io/badge/Status-Live-brightgreen?style=for-the-badge)]()

> A production-grade static website hosting setup on AWS demonstrating S3, CloudFront CDN, Cross-Region Replication, Lifecycle Rules, Access Logs, Pre-signed URLs, and custom error handling — all implemented on a real AWS account.

---

## 🔗 Live Website

| Access Method | URL |
|---|---|
| CloudFront (HTTPS) | `https://d3l2xvnnse24tu.cloudfront.net` |
| S3 Website Endpoint | `http://narendra-portfolio-26.s3-website.ap-south-1.amazonaws.com` |

---

## 📋 Table of Contents

- [Problem Statement](#-problem-statement)
- [Solution Architecture](#-solution-architecture)
- [AWS Services Used](#-aws-services-used)
- [Project Features](#-project-features)
- [Step-by-Step Implementation](#-step-by-step-implementation)
- [Screenshots](#-screenshots)
- [Key Concepts Learned](#-key-concepts-learned)
- [Interview Questions Covered](#-interview-questions-this-project-answers)
- [Author](#-author)

---

## ❓ Problem Statement

Most developers host static websites on shared hosting services that are:
- **Slow** for users far from the server
- **Not secure** — no HTTPS
- **Single point of failure** — no disaster recovery
- **Expensive** at scale
- **Unmanaged** — no logging, no lifecycle management

**This project solves all of these problems using AWS S3 + CloudFront with a full production setup.**

---

## 🏗️ Solution Architecture

```
                                               ┌─────────────────────────────────────────┐
                                               │           USERS WORLDWIDE               │
                                               └──────────────┬──────────────────────────┘
                                                              │ HTTPS Request
                                                              ▼
                                               ┌─────────────────────────────────────────┐
                                               │         AWS CLOUDFRONT CDN              │
                                               │   d3l2xvnnse24tu.cloudfront.net         │
                                               │                                         │
                                               │  • 400+ Edge Locations Worldwide        │
                                               │  • Free SSL/TLS Certificate (ACM)       │
                                               │  • HTTP → HTTPS Redirect                │
                                               │  • Custom 404 Error Page                │
                                               │  • DDoS Protection (AWS Shield)         │
                                               └──────────────┬──────────────────────────┘
                                                              │ Cache Miss → Fetch Origin
                                                              ▼
                      ┌───────────────────────────────────────────────────────────────────────────┐
                      │                    AWS S3 — ap-south-1 (Mumbai) PRIMARY                   │
                      │                         narendra-portfolio-26                             │
                      │                                                                           │
                      │  ┌─────────────────┐  ┌──────────────────┐  ┌───────────────────────┐     │
                      │  │ Static Website  │  │   Versioning     │  │   Lifecycle Rules     │     │
                      │  │ Hosting Enabled │  │   Enabled        │  │   30d → Standard-IA   │     │
                      │  │ index.html      │  │   All versions   │  │   90d → Delete        │     │
                      │  │ 404.html        │  │   tracked        │  │   Old versions        │     │
                      │  └─────────────────┘  └──────────────────┘  └───────────────────────┘     │
                      │                                                                           │
                      │  ┌─────────────────┐  ┌──────────────────┐  ┌───────────────────────┐     │
                      │  │  Bucket Policy  │  │  Pre-signed URLs │  │    Access Logging     │     │
                      │  │  Public Read    │  │  Temp Access     │  │    → logs bucket      │     │
                      │  │  GetObject      │  │  1hr expiry      │  │    All requests       │     │
                      │  └─────────────────┘  └──────────────────┘  └───────────────────────┘     │
                      └───────────────────────────────┬───────────────────────────────────────────┘
                                                      │ Cross Region Replication (CRR)
                                                      │ Automatic — Real Time
                                                      ▼
                      ┌───────────────────────────────────────────────────────────────────────────┐
                      │                  AWS S3 — us-east-1 (N. Virginia) REPLICA                 │
                      │                         narendra-portfolio-replica                        │
                      │                                                                           │
                      │              Disaster Recovery Backup — Auto Replicated                   │
                      │              If Mumbai goes down → Data safe in Virginia                  │
                      └───────────────────────────────────────────────────────────────────────────┘
                      
                      ┌───────────────────────────────────────────────────────────────────────────┐
                      │                  AWS S3 — ap-south-1 (Mumbai) LOGS                        │
                      │                         narendra-portfolio-logs                           │
                      │                                                                           │
                      │              All S3 access logs stored here                               │
                      │              Who visited, when, which file, IP address                    │
                      └───────────────────────────────────────────────────────────────────────────┘
```

---

## ☁️ AWS Services Used

| Service | Purpose |
|---|---|
| **Amazon S3** | Static website hosting, file storage |
| **Amazon CloudFront** | CDN, HTTPS, edge caching, error pages |
| **AWS Certificate Manager (ACM)** | Free SSL/TLS certificate for HTTPS |
| **S3 Cross-Region Replication** | Disaster recovery — Mumbai → Virginia |
| **S3 Versioning** | Track every file change, rollback support |
| **S3 Lifecycle Rules** | Auto cost optimization |
| **S3 Server Access Logging** | Track all requests for audit/debug |
| **S3 Pre-signed URLs** | Temporary secure access to private files |
| **IAM** | CRR role, bucket policies, least privilege |

---

## ✅ Project Features

### 1. 🪣 S3 Static Website Hosting
- Created S3 bucket in **ap-south-1 Mumbai** region
- Enabled static website hosting with `index.html` as index document
- Configured bucket policy for public read access
- Disabled Block Public Access for website serving

### 2. 🌍 CloudFront CDN + HTTPS
- Created CloudFront distribution pointing to S3 website endpoint
- **HTTP → HTTPS redirect** enforced for all users
- Free SSL certificate via AWS Certificate Manager
- Website served from nearest edge location worldwide
- Mumbai users get content from Mumbai edge — not Stockholm or Virginia

### 3. 🚫 Custom 404 Error Page
- Built branded `404.html` with animated particles
- Configured CloudFront custom error responses for 403 and 404
- Users see professional error page instead of ugly AWS default
- Error page includes navigation back to home, GitHub and LinkedIn links

### 4. 🔄 S3 Versioning
- Every file upload creates a new version
- Old versions preserved automatically
- Can rollback to any previous version at any time
- Protects against accidental deletion or overwrite

### 5. ♻️ Lifecycle Rules
- Rule: `move-old-versions-to-ia`
- Non-current versions → **Standard-IA** after **30 days** (cheaper storage)
- Non-current versions → **Permanently deleted** after **90 days**
- Automatic cost optimization — no manual work needed

### 6. 📋 S3 Server Access Logging
- All requests to portfolio bucket logged automatically
- Logs stored in separate `narendra-portfolio-logs` bucket
- Logs contain: IP address, request time, file accessed, response code
- Used for traffic analysis, security auditing, debugging

### 7. 🔐 Pre-signed URLs
- Generated temporary signed URL for `index.html`
- URL valid for 1 hour — expires automatically
- Proves concept of secure temporary access to S3 objects
- Used in real world for: invoice downloads, private document sharing, video streaming

### 8. 🌏 Cross-Region Replication (CRR)
- Source: `narendra-portfolio-26` — ap-south-1 Mumbai
- Destination: `narendra-portfolio-replica` — us-east-1 N. Virginia
- IAM role `s3crr_role_for_narendra-portfolio-26` created automatically
- New objects replicated within seconds — proved with `replication-test.txt`
- **Disaster recovery** — if Mumbai region fails, data safe in Virginia

---

## 📸 Screenshots

> All screenshots taken from real AWS account during live implementation.

| Step | Screenshot | What It Proves |
|---|---|---|
| 01 | `01-s3-bucket-created.png` | Bucket created in Mumbai ap-south-1 |
| 02 | `02-website-live-s3.png` | Portfolio live on S3 HTTP endpoint |
| 03 | `03-cloudfront-enabled.png` | CloudFront distribution status Enabled |
| 04 | `04-website-live-cloudfront.png` | Portfolio loading via CloudFront HTTPS |
| 05 | `05-custom-404-page.png` | Branded 404 page working on invalid URL |
| 06 | `06-lifecycle-rule-created.png` | Lifecycle rule move-old-versions-to-ia |
| 07 | `07-access-logs-enabled.png` | Server access logging configured |
| 08 | `08-presigned-url-working.png` | Portfolio accessed via pre-signed URL |
| 09 | `09-crr-rule-created.png` | Replication rule Mumbai → Virginia |
| 10 | `10-crr-proved-virginia.png` | replication-test.txt appeared in Virginia |

---

## 🛠️ Step-by-Step Implementation

### Phase 1 — S3 Bucket Setup
```
1. Create bucket: narendra-portfolio-26 in ap-south-1
2. Disable Block Public Access
3. Enable Versioning
4. Upload index.html + assets folder
5. Enable Static Website Hosting
6. Add Bucket Policy for public read
```

### Phase 2 — CloudFront Setup
```
1. Go to CloudFront → Create Distribution
2. Origin: narendra-portfolio-26.s3-website.ap-south-1.amazonaws.com
3. Protocol: HTTP only (S3 website endpoint is HTTP)
4. Viewer Protocol Policy: Redirect HTTP to HTTPS
5. Default Root Object: index.html
6. Plan: Free ($0/month)
7. Create Distribution → wait 5-10 min for Enabled status
```

### Phase 3 — Custom Error Pages
```
1. Upload 404.html to S3 bucket
2. CloudFront → Error Pages → Create custom error response
3. 403 → /404.html → 200 OK
4. 404 → /404.html → 200 OK
5. Test: open cloudfront-url/anythingfake
```

### Phase 4 — Lifecycle Rules
```
1. S3 → narendra-portfolio-26 → Management → Lifecycle rules
2. Create rule: move-old-versions-to-ia
3. Apply to: entire bucket
4. Noncurrent versions → Standard-IA after 30 days
5. Noncurrent versions → Delete after 90 days
```

### Phase 5 — Access Logging
```
1. Create bucket: narendra-portfolio-logs in ap-south-1
2. S3 → narendra-portfolio-26 → Properties → Server access logging
3. Enable → Destination: s3://narendra-portfolio-logs/portfolio-logs/
4. Save changes
```

### Phase 6 — Pre-signed URL
```
1. S3 → narendra-portfolio-26 → click index.html
2. Object actions → Share with a presigned URL
3. Set expiry: 1 hour
4. Create presigned URL → copy and test in browser
```

### Phase 7 — Cross-Region Replication
```
1. Create bucket: narendra-portfolio-replica in us-east-1
2. Enable Versioning on replica bucket
3. S3 → narendra-portfolio-26 → Management → Replication rules
4. Create rule: replicate-to-virginia
5. Source: entire bucket
6. Destination: narendra-portfolio-replica
7. IAM Role: Create new role (auto)
8. Test: upload replication-test.txt to Mumbai
9. Verify it appears in Virginia within 60 seconds
```

---

## 💡 Key Concepts Learned

**CloudFront vs Direct S3:**
S3 serves from one region. CloudFront caches at 400+ edge locations. Mumbai user gets content from Mumbai edge — not from Stockholm or wherever the bucket is. Result: millisecond response vs seconds.

**Cache Hit vs Cache Miss:**
First request = cache miss → CloudFront fetches from S3 and stores copy. Every request after = cache hit → served from edge location instantly. S3 never contacted again until TTL expires.

**Why S3 alone cannot give HTTPS:**
S3 static website uses a shared AWS domain. AWS cannot issue SSL certificates for shared domains. CloudFront sits in front with ACM certificate → full HTTPS.

**Why CRR needs versioning on both buckets:**
Replication works at the version level. Without versioning enabled on both source and destination, S3 cannot track which versions have been replicated and which haven't.

**Pre-signed URL vs Public URL:**
Public URL = anyone can access forever. Pre-signed URL = temporary access with expiry time, generated by IAM credentials. After expiry, link stops working even if shared.

---

## 🎯 Interview Questions This Project Answers

- What is CloudFront and how does it improve performance?
- What is the difference between cache hit and cache miss?
- Why can't S3 static website serve HTTPS directly?
- What is Cross-Region Replication and when would you use it?
- What is a pre-signed URL? How is it different from a public URL?
- What are S3 lifecycle rules and how do they help with cost optimization?
- What is S3 versioning and why is it important?
- How would you set up disaster recovery for an S3 hosted website?
- What is TTL in CloudFront?
- How do you troubleshoot a 403 error on S3 static website?

---

## 👨‍💻 Author

**Narendra Deshmukh**
AWS Cloud Engineer | Devops Enginner | Pune, Maharashtra

[![GitHub](https://img.shields.io/badge/GitHub-narendra--clouds-black?style=flat&logo=github)](https://github.com/narendra-clouds)
[![LinkedIn](https://img.shields.io/badge/LinkedIn-narendra--deshmukh--cloud-blue?style=flat&logo=linkedin)](https://www.linkedin.com/in/narendra-deshmukh-cloud/)
[![Email](https://img.shields.io/badge/Email-deshmukhn298%40gmail.com-red?style=flat&logo=gmail)](mailto:deshmukhn298@gmail.com)

> 💡 This project is part of my hands-on AWS learning series. Every step was performed on a real AWS account — no simulators. All screenshots are from live implementation.

---

⭐ **If this project helped you, please give it a star on GitHub!**
