---
title: "Deploying an AWS Lambda Function Using Terraform: A Step-by-Step Guide"
datePublished: Thu May 25 2023 22:27:22 GMT+0000 (Coordinated Universal Time)
cuid: cli3pfjju000609msd90i0z2l
slug: deploying-an-aws-lambda-function-using-terraform-a-step-by-step-guide
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/bZZp1PmHI0E/upload/f7e851fed88784dd34ab438de4c0569b.jpeg
tags: aws, devops, terraform, aws-lambda

---

As part of my new journey, I've found myself having to work with AWS Lambda, a serverless compute service that lets you run your code without the hassles of managing the underlying servers. In this post, I'll walk you through the process of how I deployed a generic Python Lambda function using Terraform, a popular infrastructure-as-code (IaC) tool.

**Setting Up Your Terraform Configuration File**

To start with, let's discuss the main components of a Terraform configuration file, which typically ends in `.tf`. The file is written in HashiCorp Configuration Language (HCL), and it tells Terraform what infrastructure to create. For our purpose, we will need to define a provider, a resource, and possibly variables.

The `provider` block tells Terraform which cloud service provider we are using, in this case, AWS. The `resource` block specifies what resources we want Terraform to manage for us, which will be an AWS Lambda function in our example.

Variables can be defined using the `variable` block. They allow us to make our configurations more flexible and reusable. In the context of AWS, we'll want to securely store our AWS Access Key ID and Secret Access Key as environment variables in our local machine to avoid hardcoding sensitive information into our Terraform file. We can access these environment variables in Terraform using `var.<variable_name>` syntax.

Here's a very simple Python script that you can use for your AWS Lambda function. It's a basic "Hello, World!" function:

```python
# lambda_function.py
def lambda_handler(event, context):
    message = 'Hello, World!'
    return {
        'statusCode': 200,
        'body': message
    }
```

You can replace `<path_to_your_python_script>` in the Terraform file with the path to this script. When the function is invoked, it will return a response with a status code of 200 and a body of "Hello, World!".

Here's a basic Terraform configuration file to deploy a Lambda function:

```apache
provider "aws" {
  region = "us-east-1"
  access_key = var.AWS_ACCESS_KEY
  secret_key = var.AWS_SECRET_KEY
}

data "archive_file" "lambda_zip" {
  type        = "zip"
  source_file = "<path_to_your_python_script>"
  output_path = "/tmp/lambda.zip"
}

resource "aws_lambda_function" "my_lambda" {
  function_name = "MyLambdaFunction"
  handler       = "lambda_function.lambda_handler"
  runtime       = "python3.8"

  filename      = data.archive_file.lambda_zip.output_path
  source_code_hash = data.archive_file.lambda_zip.output_base64sha256

  role          = aws_iam_role.lambda_exec.arn
}

resource "aws_iam_role" "lambda_exec" {
  name = "lambda_exec_role"
  
  assume_role_policy = <<EOF
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "Service": "lambda.amazonaws.com"
      },
      "Action": "sts:AssumeRole"
    }
  ]
}
EOF
}

variable "AWS_ACCESS_KEY" {}
variable "AWS_SECRET_KEY" {}
```

This Terraform configuration first defines the AWS provider and then creates a zip file of our Python script using the `archive_file` data source. Then, it defines an `aws_lambda_function` resource that represents our Lambda function and an `aws_iam_role` resource that represents the IAM role our Lambda function will assume.

To store your AWS Access Key ID and Secret Access Key, you can set them as environment variables on your local machine using the commands below:

```bash
export AWS_ACCESS_KEY=<your_access_key>
export AWS_SECRET_KEY=<your_secret_key>
```

**Deploying the Lambda Function**

Now that we've set up our environment and Terraform file, it's time to deploy our Lambda function.

The first step in any Terraform project is to initialize the working directory, which we can do with the `terraform init` command. This prepares the directory for use with Terraform, downloads the AWS provider plugin, and sets up the backend for storing our state file.

```bash
terraform init
```

Next, we'll want to validate our Terraform configuration with the `terraform validate` command. This checks that our `.tf` file is syntactically valid and internally consistent, regardless of any provided variables or existing state.

```bash
terraform validate
```

If the configuration is valid, the command will return a success message. If not, it will return error messages that help you identify the problem.

Once our configuration is validated, we can create an execution plan with the `terraform plan` command. This shows us what Terraform will do before it does it, which is useful for understanding what changes will be made to our infrastructure.

```bash
terraform plan
```

After reviewing the execution plan, we can finally apply our Terraform configuration with the `terraform apply` command. This will create our AWS Lambda function.

```bash
terraform apply
```

You'll be prompted to enter 'yes' to confirm that you want to make the changes described in the execution plan. After entering 'yes', Terraform will create the Lambda function.

**Verifying the Deployment**

After running `terraform apply`, you should be able to log into your AWS account and see the new Lambda function in the AWS Lambda console. Go ahead and test it out to make sure it's working as expected.

**Cleaning Up**

Once you've confirmed that everything is working correctly, don't forget to clean up your resources to avoid any unnecessary costs. You can delete all the resources that were created by your Terraform configuration with the `terraform destroy` command.

```bash
terraform destroy
```

Again, you'll be prompted to enter 'yes' to confirm that you want to destroy the resources. After entering 'yes', Terraform will delete the Lambda function.

**Conclusion**

I hope you found this walk-through of deploying an AWS Lambda function using Terraform helpful. It was quite a learning experience for me to write and test these steps. By going through this process, not only did I deepen my understanding of Terraform and AWS Lambda, but I also got to appreciate the power of infrastructure as code in automating and simplifying cloud deployments. I'm looking forward to using Terraform in a corporate setting and automating many of my future DevSecOps tasks.