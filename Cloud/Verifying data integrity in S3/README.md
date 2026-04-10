# Checking Data Integrity in Amazon S3 Using Additional Checksums

---

## Project Overview

This project demonstrates how to verify the integrity of data stored in Amazon S3 using additional checksum algorithms.

The objective is to ensure that uploaded objects remain uncorrupted during transfer and storage by leveraging AWS-supported checksum mechanisms.

---

## Key Concepts

Amazon S3 supports multiple checksum algorithms to validate data integrity:

- CRC32  
- CRC32C  
- SHA-1  
- SHA-256  

These checksums are calculated during upload and can be verified after the object is stored.

---

## Step 1: Create an S3 Bucket

1. Navigate to the AWS Management Console  
2. Open the Amazon S3 service  
3. Click **Create bucket**  
4. Provide:
   - Bucket name (must be globally unique)  
   - AWS Region  
5. Leave default settings or configure as required  
6. Click **Create bucket**

---

## Step 2: Upload a File with Checksum

1. Open the created S3 bucket  
2. Click **Upload** → **Add files**  
3. Select the file to upload  
4. Expand **Properties**  
5. Locate the **Checksum** section  
6. Choose a checksum algorithm:
   - SHA-256 (recommended for strong integrity validation)  
7. Proceed and click **Upload**

During upload, S3 calculates and stores the checksum value.

---

## Step 3: Verify Checksum

1. Open the uploaded object  
2. Navigate to the **Properties** tab  
3. Locate the **Checksum** section  
4. View the stored checksum value  

To verify integrity:

- Compare the checksum with the locally generated checksum (if available)  
- Confirm that the values match  

Matching values indicate that the data has not been altered or corrupted.

---

## Step 4: Clean Up

1. Delete the uploaded object:
   - Select the object  
   - Click **Delete**  

2. Delete the S3 bucket:
   - Ensure the bucket is empty  
   - Click **Delete bucket**  
   - Confirm deletion  

This prevents unnecessary storage costs.

---

## Key Features

- Ensures data integrity during upload and storage  
- Supports multiple checksum algorithms  
- Provides verification through AWS Console  
- Enhances reliability of cloud storage operations  

---

## Security Considerations

- Data integrity verification helps detect corruption or tampering  
- Combine checksums with encryption for stronger data protection  
- Use IAM policies to restrict unauthorized uploads and access  

---

## Challenges Faced

### Checksum Visibility

Understanding where to locate checksum values in the AWS Console required navigating object properties.

---

### Algorithm Selection

Choosing the appropriate checksum algorithm required balancing:

- Security strength  
- Performance considerations  

---

## Lessons Learned

- Checksums are essential for ensuring data integrity in cloud storage  
- AWS S3 provides built-in support for multiple checksum algorithms  
- Verifying data integrity is a critical step in secure cloud operations  
- Proper validation mechanisms improve reliability and trust in stored data  

---
