---
title: "Deploying AWS Lambda behind Application Load Balancer (ALB)"
datePublished: Thu Jul 27 2023 02:15:53 GMT+0000 (Coordinated Universal Time)
cuid: clkkiw8ye000209mteu6s8nta
slug: deploying-aws-lambda-behind-application-load-balancer-alb
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/z7prq6BtPE4/upload/c187d3a661f97970c8eec070a625754e.jpeg
tags: aws, python, devops, serverless

---

Recently, I was working on a project that involved the use of AWS Lambda functions with an Application Load Balancer (ALB) trigger. This is a great way to quickly create a scalable API with high availability. I followed the tutorial (AWS Developer Associate) on Udemy to learn the details of the implementation.

**First Stop - Coding with AWS Lambda in Python**

To get the ball rolling, I started by writing a Lambda function in Python that would be triggered upon a specific event from ALB. Using AWS Lambda with ALB is a powerful combination, allowing me to execute my function in response to HTTP(S) requests.

Here's a highly simplified version of the Python code snippet I wrote:

```python
def lambda_handler(event, context):
    # print the received event
    print('Received event: ', event)

    return {
        'statusCode': 200,
        'statusDescription': '200 OK',
        'isBase64Encoded': False,
        'headers': {
            'Content-Type': 'text/html'
        },
        'body': '<h1>Hello from Lambda!</h1>'
    }
```

In this function, `lambda_handler` is the entry point when the function is triggered. The parameters, `event` and `context`, are automatically passed to the function by AWS Lambda. The `event` object contains all information about the triggering event, and `context` includes runtime information about the Lambda function. In my simple example, I just print the received event and return a response with an HTTP status code, a status description, a header specifying the content type, and a body containing a short message.

**Second Stop - Configuring AWS Portal**

With my function ready, it was time to deploy and set it up in the AWS portal.

1. **Creating a Lambda Function:** I navigated to the AWS Lambda console, clicked on 'Create Function, and followed the wizard. I chose 'Author from scratch', provided a name for my function, selected Python as the runtime, and defined the necessary permissions. Then, I uploaded my Python script.
    
2. **Setting up an ALB Trigger:** Next, I headed over to the EC2 console to set up the ALB. In the 'Load Balancing' section, I created a new Load Balancer, choosing the 'Application Load Balancer' option, and configuring it according to my application's needs. Importantly, I added a listener that points to my Lambda function.
    
3. **Testing the Setup:** With the function and ALB ready, it was time to test. I sent a request to the ALB's DNS name and was delighted to see my Lambda function execute flawlessly and return the expected HTTP response.
    

Keep in mind that while this process seems straightforward, there may be several key points along the way, such as setting up IAM roles, security groups, other ALB configurations, and more.

**The Takeaway**

AWS Lambda, when set as a target for ALB, can help power microservices, web applications, and API backends that can scale automatically, reducing the need to provision and manage servers. This setup also allows the decoupling of application components, enabling independent scaling, development, and updating, thereby promoting a more efficient and robust application lifecycle.

I find it quite interesting how serverless capabilities can be useful in managing and securing CI/CD pipelines by efficiently aggregating and processing logs from multiple sources, triggering automated responses, and notifying the accountable teams.  
  
As a next step, I would like to delve into AWS Lambda integrations with other AWS services like Amazon Cloudwatch, AWS Step Functions, Amazon S3, etc.