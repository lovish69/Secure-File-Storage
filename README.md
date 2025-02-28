# Secure File Storage Using AWS S3 & CloudFront
📌 Project Overview

This project implements a secure file storage system using Amazon S3, AWS CloudFront, and IAM policies. The primary goal is to store files securely in S3, restrict direct access, and deliver files using CloudFront for improved security and performance.

🔹 Key Features:
✅ Securely store files in S3.
✅ Block direct S3 access using IAM policies.
✅ Use CloudFront for content distribution

📊 Architecture Diagram

![securestoragedrawio](https://github.com/user-attachments/assets/f3897953-5fd9-40df-ace0-52dc05275c77)

🛠 AWS Services Used
1. Amazon S3 (Simple Storage Service)

    Used for secure storage of files.
    Configured to block public access to prevent unauthorized access.

2. AWS IAM (Identity & Access Management)

    Used to create fine-grained access control policies.
    Ensured only CloudFront could fetch files from S3.

3. AWS CloudFront (Content Delivery Network)

    Configured to fetch files from S3 securely.
    Had minor permission issues, but setup steps are included.

![Screenshot (718)](https://github.com/user-attachments/assets/9b1014b8-c32f-4fbc-b3fd-c1b8b746b964)

   
🔹 Step-by-Step Implementation
Step 1: Create an S3 Bucket

First, create a private S3 bucket where files will be stored securely.
```bash   
aws s3api create-bucket --bucket secure-file-storage-mumbai --region ap-south-1
```

![Screenshot (719)](https://github.com/user-attachments/assets/e87ba11b-bc86-4432-9a77-627a6d9d9a2f)

Step 2: Block Public Access to the S3 Bucket

To ensure that files are only accessed by authorized users, block all public access to the bucket:
```bash
aws s3api put-public-access-block \
    --bucket secure-file-storage-mumbai \
    --public-access-block-configuration BlockPublicAcls=true,IgnorePublicAcls=true,BlockPublicPolicy=true,RestrictPublicBuckets=true
```
Step 3: Upload a Test File to S3

Now, let's upload a file to our secure S3 bucket.
```bash
aws s3 cp myfile.txt s3://secure-file-storage-mumbai/
```
![Screenshot (725)](https://github.com/user-attachments/assets/2f9c7ad4-ef7a-4438-b5e6-94f77108f75a)

![Screenshot (728)](https://github.com/user-attachments/assets/4c9da2ff-a4d6-4abe-8147-7815e35507a8)


Step 4: Create an IAM Policy for CloudFront to Access S3

To ensure that only CloudFront can fetch files from S3, we create an IAM policy:
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "Service": "cloudfront.amazonaws.com"
      },
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::secure-file-storage-mumbai/*",
      "Condition": {
        "StringEquals": {
          "AWS:SourceArn": "arn:aws:cloudfront::941377118990:distribution/E13AO860FYAIF8"
        }
      }
    }
  ]
}
```

![Screenshot (721)](https://github.com/user-attachments/assets/e0d21559-5e0e-45d4-ad94-4ab4fda5002b)

![Screenshot (724)](https://github.com/user-attachments/assets/d5deaf39-e9bb-424a-9804-2b4c350349a5)

![Screenshot (729)](https://github.com/user-attachments/assets/4100b580-8bcd-4b81-a63d-fa51f93c1dcd)



Step 5: Create a CloudFront Distribution

Now, we configure CloudFront to securely deliver files from S3.
```bash
aws cloudfront create-distribution --origin-domain-name secure-file-storage-mumbai.s3.amazonaws.com
```

Step 6: Test File Access via CloudFront

To verify CloudFront’s integration with S3, try accessing a file:
```bash
curl -I https://YOUR_CLOUDFRONT_DOMAIN/testfile.txt
```
 Expected Results:
✅ If CloudFront is correctly configured, it will fetch the file from S3.
❌ If permissions are incorrect, Access Denied will appear.



