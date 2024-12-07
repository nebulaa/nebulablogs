---
title: "My Experience Performing a Threat Model Review of an AWS Architecture Plan"
datePublished: Tue Sep 26 2023 22:52:54 GMT+0000 (Coordinated Universal Time)
cuid: cln0wy0ju000108l6dt2xdpm4
slug: my-experience-performing-a-threat-model-review-of-an-aws-architecture-plan
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/PBZsk31orGM/upload/0ec2b68f8c0c70bb9d058027517a06e6.jpeg
tags: aws, security, devsecops

---

Shortly after passing the AWS (Amazon Web Services) Developer Associate exam, I was tasked with conducting a security threat model review of a new microservice architecture design plan. The development team wanted to integrate existing Kubernetes cluster-based services with serverless components such as AWS Lambda, DynamoDB, SQS and Data streams. The review led me to learn the AWS recommended security best practices when it comes to utilizing these services.

In this blog post, I'll share my experience performing a threat model review, using a hypothetical AWS architecture plan as a backdrop. The plan incorporates AWS Lambda, Amazon EKS, Athena queries with S3 buckets, DynamoDB, MySQL, and SQS as the key components.

#### **Step 1: Understanding the AWS System**

The journey begins with a deep dive into the AWS architecture plan. In our scenario, this plan encompasses several vital components:

1. **AWS Lambda:** A serverless compute service.
    
2. **Amazon EKS:** A managed Kubernetes service.
    
3. **Athena Queries with S3 Buckets:** The capability to query data stored in S3 using Athena.
    
4. **DynamoDB:** A NoSQL database service.
    
5. **MySQL:** A relational database service.
    
6. **Amazon SQS:** A managed message queuing service.
    

#### **Step 2: Identifying Assets and Data Flows**

To initiate the threat model review, I carefully looked over the plan to identify the assets within the system and to figure out all the details of data flows among these assets.

The documented assets and the data flow among them were converted to a threat model diagram showing the protocol used for communication (typically HTTPS).

#### **Step 3: Enumerating Threats**

The heart of the review process lies in enumerating potential threats within this AWS environment. Identifying common threats, such as:

* **Unauthorized Access:** The risk of unauthorized entities or users gaining access to critical AWS resources.
    
* **Data Exfiltration:** The potential for unauthorized extraction of sensitive data.
    
* **Data Tampering:** Concerns about unauthorized modifications to data.
    
* **Resource Exhaustion:** The specter of overutilization leading to service degradation.
    
* **Denial of Service (DoS):** The challenge of safeguarding against disruptions to service availability.
    
* **Insecure Dependencies:** Vigilance regarding vulnerabilities in third-party libraries or services.
    

#### **Step 4: Risk Assessment**

Evaluating the probability and potential impact of each identified threat is a cornerstone of the threat model review process. It is crucial to balance security measures with performance metrics. For example, the risk assessment must consider the criticality of the data before suggesting encryption. The service degradation or costs associated with querying encrypted data stores outweigh the risk of losing non-critical data.

#### **Step 5: Implementing Mitigation Strategies**

Based on the risk assessment, formulate mitigation strategies. Some examples include:

* **Balancing Encryption and Performance:** Employ AWS Key Management Service (KMS) to selectively encrypt data. Fine-tune encryption settings to strike a balance between security and query performance.
    
* **Unauthorized Access:** Implement rigorous IAM policies, enforce Multi-Factor Authentication (MFA), and utilize AWS security groups.
    
* **Data Exfiltration:** Implement encryption for data at rest and in transit, and manage cryptographic keys using KMS.
    
* **Resource Exhaustion:** Leverage AWS Auto Scaling to dynamically adjust resource allocation.
    
* **Denial of Service (DoS):** Utilize AWS Shield for DDoS protection and configure rate limiting.
    
* **Managing Insecure Dependencies:** Regularly update and patch third-party libraries and services.
    

#### **Step 6: Monitoring and Incident Response**

A backup and disaster recovery plan needs to be baked into the design architecture to ensure high availability

The establishment of continuous monitoring practices and an incident response plan was a crucial aspect of this review. AWS CloudWatch acted as a monitoring instrument. The incident response plan guarantees preparedness for any unanticipated security incidents.

#### **Step 7: Documentation and Training**

Microsoft's threat modeling tool was used in my review documentation, but I am hoping to find better alternatives soon. I ensured that the threat model, security controls, and incident response procedures were thoroughly documented.

#### Additional suggestions (if the company has already embraced a DevOps culture):

#### **Identifying Vulnerable Dependencies in CI/CD**

Incorporate vulnerability identification into the CI/CD pipeline with a particular focus on image or container scanning services:

* **Image Scanning:** Integrate services like AWS Elastic Container Registry (ECR) or third-party tools to scan container images for vulnerabilities before deployment.
    
* **Dependency Scanning:** Employ tools like Snyk to pinpoint vulnerabilities in code dependencies.
    
* **CI/CD Integration:** Automate vulnerability scans as an integral part of the CI/CD process, enabling the detection of issues early in the development cycle.
    
* **Continuous Monitoring:** Maintain up-to-date databases of the identified risks and regularly re-scan images to record newly discovered vulnerabilities.
    

#### **Conclusion:**

Incorporating feedback from the development and operations teams and continuously improving the security recommendations will greatly benefit all teams involved and foster a healthy learning environment. That was a key takeaway from my experience working on this project.