---
title: "Running Dockerized Trivy on AWS Lambda for Automated Security Compliance Reporting - DevSecOps Project"
datePublished: Fri Apr 05 2024 00:37:11 GMT+0000 (Coordinated Universal Time)
cuid: clulxrtsd000208l7bph12a94
slug: running-dockerized-trivy-on-aws-lambda-for-automated-security-compliance-reporting-devsecops-project
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/TbuescuqMjA/upload/1813a000dab03d1e213e7862d79eba03.jpeg
tags: aws, python, security, devops, aws-lambda

---

In this blog post, we'll delve into the powerful capabilities of Trivy, an open-source vulnerability scanner, to identify misconfigurations within AWS environments. Specifically, we'll focus on assessing adherence to the AWS CIS 1.4 (Center for Internet Security) standards. By harnessing Trivy alongside AWS Lambda, we'll demonstrate how to automate the process of conducting security compliance scans and generating detailed reports.

To create the docker image, we need:

1. app.py - Python file with the required lambda\_handler function.
    
2. requirements.txt - Python dependency - boto3==1.34.64
    
3. Dockerfile
    

**Python code to create the lambda function:**

```python
import json
import subprocess
import boto3
from datetime import datetime

s3_client = boto3.client("s3")
sts_client = boto3.client("sts")
time = datetime.now().strftime("%d-%B-%Y-%H-%M-%S")


def generate_aws_compliance_report(command, s3_file_name):
    output_file = "/tmp/aws_compliance_results.txt"
    with open(output_file, "w", encoding="utf-8") as f:
        subprocess.run(command, check=True, stdout=f)

    if upload_file_to_s3(output_file, s3_file_name):
        return True
    return False


def generate_trivy_misconfig_report(command, s3_file_name):
    output_file = "/tmp/trivy_misconfig_results.txt"
    with open(output_file, "w", encoding="utf-8") as f:
        subprocess.run(command, check=True, stdout=f)

        if upload_file_to_s3(output_file, s3_file_name):
            return True
        return False


def upload_file_to_s3(output_file, file_name):
    s3_client.upload_file(output_file, "trivy-reports-test", f"{file_name}")
    return True


def lambda_handler(event, context):
    try:
        body = event["body"]
        account_id = body.get("account_id")
        region_name = body.get("region_name")
        service_name = body.get("service_name")
        format_type = body.get("format_type")
        severity_level = body.get("severity_level")
        aws_compliance_scan = body.get("aws_compliance_scan")
        aws_compliance_standard = body.get("aws_compliance_standard")

        if account_id is None:
            account_id = sts_client.get_caller_identity().get("Account")

        if region_name is None:
            region_name = "us-east-1"

        if service_name is None:
            service_name = "accessanalyzer,api-gateway,athena,cloudfront,cloudtrail,cloudwatch,codebuild,documentdb,dynamodb,ec2,ecr,ecs,efs,eks,elasticache,elasticsearch,elb,emr,iam,kinesis,kms,lambda,mq,msk,neptune,rds,redshift,s3,sns,sqs,ssm,workspaces"

        if format_type is None:
            format_type = "json"

        if severity_level is None:
            severity_level = "MEDIUM,HIGH,CRITICAL"

        if aws_compliance_standard is None:
            aws_compliance_standard = "aws-cis-1.4"

        command = [
            "trivy",
            "aws",
            "-q",
            "--account",
            account_id,
            "--region",
            region_name,
            "--service",
            service_name,
            "--format",
            format_type,
            "--severity",
            severity_level,
        ]

        if aws_compliance_scan:
            command.extend(["--compliance", aws_compliance_standard])
            s3_file_name = f"{account_id}/{region_name}/{aws_compliance_standard}/{service_name}/{time}/report.{format_type}"
            if generate_aws_compliance_report(
                command,
                s3_file_name,
            ):
                return {
                    "statusCode": 200,
                    "body": json.dumps(
                        {
                            "message": "AWS Compliance report generated successfully",
                            "file_upload": True,
                            "s3_file_name": s3_file_name,
                        }
                    ),
                }

            return {
                "statusCode": 500,
                "body": json.dumps(
                    {
                        "message": "AWS Compliance report generation failed",
                        "file_upload": False,
                    }
                ),
            }
        if not aws_compliance_scan:
            s3_file_name = f"{account_id}/{region_name}/AWS_misconfigurations/{service_name}/{time}/results.{format_type}"
            if generate_trivy_misconfig_report(
                command,
                s3_file_name,
            ):
                return {
                    "statusCode": 200,
                    "body": json.dumps(
                        {
                            "message": "Trivy Misconfigurations report generated successfully",
                            "file_upload": True,
                            "s3_file_name": s3_file_name,
                        }
                    ),
                }
            return {
                "statusCode": 500,
                "body": json.dumps(
                    {
                        "message": "Trivy Misconfigurations report generation failed",
                        "file_upload": False,
                    }
                ),
            }
    except Exception as e:
        print(f"An error occurred: {e}")
        return {
            "statusCode": 500,
            "body": json.dumps({"message": "Request failed"}),
        }
```

**Dockerfile:**

```yaml
FROM public.ecr.aws/lambda/python:3.11
COPY requirements.txt ${LAMBDA_TASK_ROOT}
RUN pip install -r requirements.txt
RUN yum -y install tar gzip curl
RUN curl -sfL https://raw.githubusercontent.com/aquasecurity/trivy/main/contrib/install.sh | sh -s -- -b /usr/local/bin v0.49.1
COPY app.py ${LAMBDA_TASK_ROOT}
CMD ["app.lambda_handler"]
```

The provided code snippet and Dockerfile illustrate an automated approach to generate security compliance reports within AWS Lambda. Let's break down the components:

1. **AWS Lambda Function**: The core logic resides within the `lambda_handler` function. This function is invoked in response to an event, where the event payload contains parameters such as account ID, region name, service name, format type, severity level, and more.
    
2. **Trivy**: Trivy is a popular open-source vulnerability scanner for containers. In this setup, Trivy is used to scan AWS resources for vulnerabilities and misconfigurations.
    
3. **Boto3**: Boto3 is the AWS SDK for Python. It's utilized here to interact with AWS services such as S3 and STS (Security Token Service).
    
4. **Dockerfile**: The Dockerfile defines the environment for the AWS Lambda function, including dependencies like Python packages and Trivy installation.
    

**How It Works**

1. **Event Processing**: Upon invocation, the Lambda function processes the incoming event payload, extracting parameters for the compliance scan.
    
2. **Parameter Handling**: If certain parameters are missing (e.g., account ID, region name), default values are assigned to ensure the scan proceeds smoothly.
    
3. **Trivy Scan Invocation**: Trivy is invoked with appropriate parameters based on the event payload. Trivy conducts vulnerability and misconfiguration scans on AWS resources.
    
4. **Report Generation**: Trivy generates scan results, which are written to temporary files (`aws_compliance_results.txt` or `trivy_misconfig_results.txt`).
    
5. **Upload to S3**: After the scan, the generated report is uploaded to an S3 bucket. The S3 path includes relevant metadata like account ID, region, timestamp, and compliance standard.
    
6. **Response**: Depending on the success or failure of the scan and upload process, the Lambda function returns an appropriate HTTP response along with a JSON payload containing relevant information.
    

In addition to exploring Trivy's capabilities and automating security compliance scans, we'll also cover the essential steps for building and deploying the solution. First, we'll walk through building the Docker image with the following commands:

```bash

docker build -t trivy:v1 . --platform=linux/amd64
docker tag trivy:v2 your_repository/trivy:v1
docker push your_repository/trivy:v1
```

Once the Docker image is built and pushed to a container registry, we'll proceed with the deployment steps. This involves provisioning an AWS Lambda function and ensuring it has read-only access to the AWS environment that needs to be scanned. By granting read-only access, the Lambda function can retrieve necessary information about the resources for generating Trivy reports.

The deployment process typically involves creating the Lambda function using AWS Management Console or AWS CLI, configuring its execution role with appropriate permissions (e.g., read-only access via IAM policies), and specifying triggers or event sources to invoke the function (e.g., scheduled events or API Gateway endpoints).

Ensuring secure and minimal permissions for the Lambda function is crucial to mitigate any potential risks associated with unauthorized access or unintended modifications to AWS resources.

**Benefits of Automating Security Audits and Compliance Checks**

* **Efficiency**: Automation reduces manual effort in conducting security compliance checks, enabling teams to focus on higher-value tasks.
    
* **Consistency**: Automated scans ensure consistent adherence to security standards across all resources and environments.
    
* **Timeliness**: With automated scheduling or event-driven triggers, security scans can be conducted regularly or in response to changes, enhancing overall security posture.
    
* **Visibility**: Generated reports provide insights into vulnerabilities and misconfigurations, facilitating informed decision-making and remediation efforts.
    

**Conclusion**

Automated security compliance reporting is essential for maintaining robust security practices in cloud environments. By leveraging AWS Lambda alongside tools like Trivy and AWS storage services such as S3, organizations can streamline security operations, mitigate risks, and ensure compliance with industry standards. The provided code can serves as a foundation for building scalable, more comprehensive and efficient security compliance workflows within AWS Lambda.