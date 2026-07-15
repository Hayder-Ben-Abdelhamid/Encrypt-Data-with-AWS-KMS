# Secure Data Encryption with AWS KMS & DynamoDB

**Project Link:** [View Project](http://nextwork.ai/projects/aws-security-kms)

**Author:** Hayder Ben Abdelhamid  
**Email:** haider.abdelhamid@gmail.com  
**Role:** Cybersecurity Engineering Student

---

![Image](http://nextwork.ai/content_amber_heroic_karearea/uploads/aws-security-kms_w0x1y2z3)

## 📌 Project Overview

This project demonstrates the practical application of data-at-rest encryption to secure sensitive cloud data asset layers. The primary objective is to provision cryptographic keys using **AWS Key Management Service (KMS)**, implement envelope encryption on an **Amazon DynamoDB** table, and perform comprehensive access control validation using granular **AWS Identity and Access Management (IAM)** permission policies.

### 🛠️ Toolchain & Core Concepts
* **AWS Services Utilized:** AWS Key Management Service (KMS), Amazon DynamoDB, AWS Identity and Access Management (IAM).
* **Core Competencies:** Cryptographic Key Lifecycle Management, Transparent Data Encryption (TDE), Cryptographic Action-Based Permissions, Least Privilege Verification.

---

## 🔑 Cryptographic Foundations & AWS KMS

Encryption transforms raw, readable **plaintext** into an unreadable **ciphertext** format using mathematical algorithms, ensuring data confidentiality against unauthorized actors. The behavior of these cryptographic algorithms is dictated by encryption keys.

AWS KMS acts as a highly secure, centralized vault for generating, managing, and auditing cryptographic keys. Because compromised keys expose the underlying data, robust key management systems are critical to an organization's defense-in-depth posture.

### Symmetric vs. Asymmetric Cryptography
* **Symmetric Encryption (Selected):** Uses the exact same shared secret key for both encryption and decryption tasks. This was selected for its performance efficiency in database storage encryption.
* **Asymmetric Encryption:** Utilizes a mathematically linked public/private key pair (where the public key encrypts data and the private key decrypts it). This approach is typically favored for asymmetric digital signatures or secure external envelope transfers.

![Image](http://nextwork.ai/content_amber_heroic_karearea/uploads/aws-security-kms_a2b3c4d5)

---

## 🗄️ Database Encryption Setup

The generated customer-managed KMS key was deployed to safeguard data housed within **Amazon DynamoDB**—a fully managed, high-performance NoSQL database built for single-digit millisecond latency at scale.

### DynamoDB Encryption Key Strategies
DynamoDB provides three distinct options for handling encryption-at-rest:
1. **AWS Owned Key:** Default encryption managed entirely by DynamoDB. It is free of charge, but provides no visibility or policy control to the customer.
2. **AWS Managed Key:** Generated inside the customer account and managed by AWS on the user's behalf.
3. **Customer Managed Key (Selected):** Created, owned, and fully controlled by the developer. This strategy provides total auditing visibility, custom rotation schedules, and granular key policy control.

![Image](http://nextwork.ai/content_amber_heroic_karearea/uploads/aws-security-kms_q8r9s0t1)

---

## 👁️ Data Visibility & Transparent Encryption Architecture

AWS KMS diverges from traditional access control methods by managing user privileges via **cryptographic action-based authorization** rather than simple key visibility. An entity may have permissions to "view" a key metadata index, but it will remain unable to read any protected data objects unless explicitly granted the `kms:Decrypt` action payload.

### Transparent Data Encryption (TDE)
During initial testing under an administrative context, table items remained visible despite active encryption. This occurs because DynamoDB leverages **Transparent Data Encryption**:
* When an authorized identity requests data, DynamoDB handles the backend interactions with AWS KMS, automatically decrypting the ciphertext payload on the fly.
* To the authorized end user, the data retrieval process appears seamless and unaltered.

![Image](http://nextwork.ai/content_amber_heroic_karearea/uploads/aws-security-kms_c0d1e2f3)

---

## 🚫 Access Control Validation (Deny Phase)

To validate the defensive boundary established by the KMS configurations, a dedicated test IAM user was provisioned to simulate an unauthorized or compromised identity.

### IAM Policy Configuration
* **Granted Permission:** `AmazonDynamoDBFullAccess` (Full data plane control over table structures and query mechanisms).
* **Withheld Permission:** Explicitly restricted from exercising `kms:Decrypt` or `kms:Encrypt` actions on the target Customer Managed Key.

### Verification Outcome
When attempting to scan or query table items using the restricted test user credentials, the AWS console returned a firm **Access Denied** error message. Even though the user possessed full structural permission to access DynamoDB, the underlying data remained completely protected due to the lack of cryptographic decryption authorization.

![Image](http://nextwork.ai/content_amber_heroic_karearea/uploads/aws-security-kms_w0x1y2z3)

---

## 🚀 Advanced Verification: Granting Cryptographic Access

To complete the access lifecycle validation loop, the test user was formally promoted to an authorized consumer within the KMS control pane.

### Resource Policy Refinement
The target KMS Key Policy was appended to include the test user account principal (`nextwork-kms-user`), explicitly permitting the following cryptographic operations:
* `kms:Encrypt`
* `kms:Decrypt`
* `kms:ReEncrypt*`

```json
{
  "Sid": "Allow Use of the Key",
  "Effect": "Allow",
  "Principal": {
    "AWS": "arn:aws:iam::ACCOUNT_ID:user/nextwork-kms-user"
  },
  "Action": [
    "kms:Encrypt",
    "kms:Decrypt",
    "kms:ReEncrypt*"
  ],
  "Resource": "*"
}
```

### Final Execution Results
Upon refreshing the test session, the IAM user successfully bypassed the access boundaries and read the underlying database data items clearly. This verified that updating the KMS resource key policy successfully restored plaintext visibility.

### Defense-in-Depth Takeaway
Encryption secures the **data asset layer directly**, distinct from the encompassing infrastructure resource container. By combining data encryption with perimeter tools (such as Security Groups, Network ACLs, and restrictive IAM policies), engineers build an optimal two-layer defense-in-depth security architecture—protecting environments at both the resource edge and the raw data payload boundaries.

![Image](http://nextwork.ai/content_amber_heroic_karearea/uploads/aws-security-kms_feffb2fb8)

---

## 📝 Project Reflections & Key Metrics

* **Time Investment:** Completed within approximately 1 hour of active engineering and deployment testing.
* **Core Challenges:** Dissecting the decoupled nature of data-plane cryptographic permissions versus standard resource-plane service permissions.
* **Key Takeaway:** Solidified understanding of the security workflows required to securely leverage customer-managed KMS infrastructure, establishing strict compliance bounds for production-level cloud ecosystems.

---
*Built in collaboration with [NextWork](http://nextwork.ai)*
