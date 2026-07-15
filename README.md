<img src="https://cdn.prod.website-files.com/677c400686e724409a5a7409/6790ad949cf622dc8dcd9fe4_nextwork-logo-leather.svg" alt="NextWork" width="300" />

# Encrypt-Data-with-AWS-KMS
I demonstrate using encryption to secure data. The goal is to create encryption keys with AWS KMS(Key Management Service), encrypt a DynamoDB table's date with that key, then test access using IAM users.


**Project Link:** [View Project](http://nextwork.ai/projects/aws-security-kms)

**Author:** Hayder Ben Abdelhamid  
**Email:** haider.abdelhamid@gmail.com

---

![Image](http://nextwork.ai/content_amber_heroic_karearea/uploads/aws-security-kms_w0x1y2z3)

---

## Introducing Today's Project!

In this project, I will demonstrate using encryption to secure data. The goal is to create encryption keys with AWS KMS(Key Management Service), encrypt a DynamoDB table's date with that key, then test access using IAM users.

### Tools and concepts

Services I used include AWS KMS (Key Management Service), DynamoDB, AWS IAM. Key concepts I learnt include encryption, database tables, KMS using permission to actions rather than just access to the key itself and creating a user to test access.

### Project reflection

This project took me approximately 1 hoursThe most challenging part was understanding how encryption works differently from other access controls . It was most rewarding to see the test user get access to encryption.

I chose to do this project today to learn all about encryption, securing data and how it actually works.

---

## Encryption and KMS

Encryption is the process of turning original/plaintext datainto a secure format. Companies and developers do this to secure their data from unauthorized users. Encryption keys are the secure code that informs an algorithm on how should encrypt.

AWS KMS is a vault for our encryption keys. Key management systems are important because they help us secure and manage the keys we use to encrypt data . Unauthorized access to the key = exposing our data, which puts our security at risk.

Encryption keys are broadly categorized as symmetric and asymetric. I set up a symmetric key because i will be using the exact same key to encrypt and decrypt my data.
Asymmetric keys would be a good choice if we need different keys for de/encryption .

![Image](http://nextwork.ai/content_amber_heroic_karearea/uploads/aws-security-kms_a2b3c4d5)

---

## Encrypting Data

My encryption key will safeguard data in DynamoDB, which is a fast and flexible AWS database service. 
DynamoDB is great for applications that need fast access to large amounts of data e.g. Gaming.

The different encryption options in DynamoDB include DynamoDB-owned,  AWS managed and customer managed.
Their differences are based on who creates and manages the key and wether we have visibility. I selected customer managed key option to use our created KMS key

![Image](http://nextwork.ai/content_amber_heroic_karearea/uploads/aws-security-kms_q8r9s0t1)

---

## Data Visibility

Rather than controlling who has access to the key, KMS manages user permissions by controllingwho has access to the key manager user permissions by controlling the actions that people can do with that key. 
In our case, even if we gave our test user the permission to see the key, it would need permission to decrypt.

Despite encrypting my DynamoDB table, I could still see the table's items because i'm the user of the key. DynamoDB uses transparent data encryption, which means it does the encryption/decryption process for us because it knows we're authorized.

![Image](http://nextwork.ai/content_amber_heroic_karearea/uploads/aws-security-kms_c0d1e2f3)

---

## Denying Access

I configured a new IAM user to validate whether unauthorized users can access encrypted data. The permission policies I granted this user are DynamoDB full acces, but Not encryption/decryption permission with AWS KMS.

After accessing the DynamoDB table as the test user, I encountered an access denief error message because our test user has no access to dencryption. This confirmed that encryption keys can be used to secure data .

![Image](http://nextwork.ai/content_amber_heroic_karearea/uploads/aws-security-kms_w0x1y2z3)

---

## EXTRA: Granting Access

To let my test user use the encryption key, I made it a key user in the KMS console! 
My key's policy was updated to allow the nextwork-kms-user to encrypt, decrypt, re-encrypt using the key.

Using the test user, I retried accessing the DynamoDB table. I observed that the user can see the data inside again, which confirmed that making it a key user is an effective way to authorize someone to see encrypted data.

Encryption secures data instead of an entire resource or service. I could combine encryption with other access control tools like security groups and permission policies  to have two layers of security the resource level, and then the data level.

![Image](http://nextwork.ai/content_amber_heroic_karearea/uploads/aws-security-kms_feffb2fb8)

---

---
