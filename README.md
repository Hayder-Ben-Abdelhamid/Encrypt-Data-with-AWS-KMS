# Secure Data Encryption with AWS KMS & DynamoDB

Author: Hayder Ben Abdelhamid
Email: haider.abdelhamid@gmail.com
Role: Cybersecurity Engineering Student 

This document extracts the technical operational steps and commands required to set up customer-managed encryption keys in AWS KMS, apply them to a DynamoDB table, and validate access boundaries via IAM policies.

---

## 1. Environment Identity Setup

Before executing AWS resource actions, configure your engineering workspace identity constraints locally:

```bash
# Check current configurations
git config --global user.name "Hayder Ben Abdelhamid"
git config --global user.email "haider.abdelhamid@gmail.com"
```

---

## 2. AWS KMS Key Policy Configuration

When configuring a **Customer Managed Key** via the AWS Console or CLI, the resource tracking policy must explicitly specify cryptographic execution rights. To authorize a test user profile, append the following statement array to your KMS key JSON policy:

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
    "kms:ReEncrypt*",
    "kms:DescribeKey"
  ],
  "Resource": "*"
}
```

---

## 3. IAM Test User Setup Policies

To replicate security permission isolation boundaries, validation flows rely on two distinct state permissions attached to the testing principal.

### Phase 1: Deny Verification (No Cryptographic Access)
Attach the standard managed data policy to grant database structural control, without adding any KMS action mappings:

* **Policy Attachment:** `AmazonDynamoDBFullAccess`

### Phase 2: CLI Access Verification Check
To run validation statements from a victim or test environment terminal, authenticate the session using target credentials:

```bash
# Configure programmatic session access profiles
aws configure --profile kms-test-user
# Inputs required: AWS Access Key ID, Secret Access Key, Default region (e.g., us-east-1)
```

---

## 4. Operational Validation Commands

Use the AWS CLI tools to audit visibility behaviors between authorized context layers and restricted user parameters.

### Read Database Entries (Authorized Administrator Context / Transparent Encryption)
```bash
# Scan items natively on the target protected table
aws dynamodb scan --table-name SecureProjectTable
```

### Read Database Entries (Restricted User Context - Phase 1)
```bash
# Execute reading under unauthorized token profiles to trigger AccessDenied Exception
aws dynamodb scan --table-name SecureProjectTable --profile kms-test-user
```
*Expected Outcome:* `An error occurred (AccessDeniedException) when calling the Scan operation: User is not authorized to perform: kms:Decrypt...`

### Read Database Entries (Authorized User Context - Phase 2)
*Execute after updating the KMS key resource policy to include the user principal:*
```bash
# Re-evaluate table scan capability
aws dynamodb scan --table-name SecureProjectTable --profile kms-test-user
```
*Expected Outcome:* Plaintext JSON query response values successfully return.
