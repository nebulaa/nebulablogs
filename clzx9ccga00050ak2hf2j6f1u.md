---
title: "Creating a Simple Python App to Demonstrate CRUD Functionality using Azure Functions"
datePublished: Fri Aug 16 2024 22:05:04 GMT+0000 (Coordinated Universal Time)
cuid: clzx9ccga00050ak2hf2j6f1u
slug: creating-a-simple-python-app-to-demonstrate-crud-functionality-using-azure-functions
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/mfSY12Au5o4/upload/f44026bf8627bec74d14e697baa0d4c2.jpeg
tags: python, azure

---

## Introduction

Azure Functions is a serverless compute service that enables you to run event-triggered code without having to explicitly provision or manage infrastructure. This tutorial will guide you through creating a simple Python app that demonstrates CRUD (Create, Read, Update, Delete) functionality using Azure Functions. Additionally, we will cover local testing using Azure Functions Core Tools.

## Step-by-Step Guide

### Step 1: Install Azure Functions Core Tools

First, ensure that you have Azure Functions Core Tools installed on your machine. You can install it by following the instructions (for your OS) here: [https://learn.microsoft.com/en-us/azure/azure-functions/functions-run-local?tabs=linux%2Cisolated-process%2Cnode-v4%2Cpython-v2%2Chttp-trigger%2Ccontainer-apps&pivots=programming-language-python](https://learn.microsoft.com/en-us/azure/azure-functions/functions-run-local?tabs=linux%2Cisolated-process%2Cnode-v4%2Cpython-v2%2Chttp-trigger%2Ccontainer-apps&pivots=programming-language-python)

For Linux distros:

```bash
sudo apt-get update
sudo apt-get install azure-functions-core-tools-4
```

### Step 2: Initialize Your Azure Functions Project

Create a new directory for your project and navigate into it:

```bash
mkdir azure-demo
cd azure-demo
```

Initialize a new Azure Functions project:

```bash
func init azure-functions-crud --worker-runtime python --model V2
```

### Step 3: Create HTTP Trigger Functions

Inside your project directory, create new HTTP trigger functions for each CRUD operation. You can create functions using the following command:

```bash
func new --template "Http Trigger" --name MyHttpTrigger
```

### Step 4: Implement CRUD Operations

Replace the content of the generated `function_app.py` file with the following code to implement the CRUD operations:

```python
import azure.functions as func
import logging
import json

app = func.FunctionApp(http_auth_level=func.AuthLevel.ANONYMOUS)

tasks = []

@app.route(route="add-task", methods=["POST"])
def add_task(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request to add a new task.')

    try:
        req_body = req.get_json()
        task_id = len(tasks) + 1
        task = {
            'id': task_id,
            'title': req_body.get('title')
        }
        tasks.append(task)
        return func.HttpResponse(json.dumps(task), status_code=201, mimetype="application/json")
    except ValueError:
        return func.HttpResponse("Invalid input", status_code=400)

@app.route(route="get-task", methods=["GET"])
def get_task(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request to get task by id.')

    try:
        task_id = int(req.params["id"]) - 1
        return func.HttpResponse(json.dumps(tasks[task_id]), status_code=200, mimetype="application/json")
    except ValueError:
        return func.HttpResponse("Invalid input", status_code=400)
    
@app.route(route="update-task", methods=["PUT"])
def update_task(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request to update task by id.')

    try:
        task_id = int(req.params["id"]) - 1
        req_body = req.get_json()
        tasks[task_id]['title'] = req_body.get('title')
        return func.HttpResponse(json.dumps({"message": "Updated", "task": tasks[task_id]}), status_code=200, mimetype="application/json")
    except ValueError:
        return func.HttpResponse("Invalid input", status_code=400)

@app.route(route="delete-task", methods=["DELETE"])
def delete_tasks(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request to delete task by id.')

    try:
        task_id = int(req.params["id"]) - 1
        tasks.pop(task_id)
        return func.HttpResponse("Deleted", status_code=200, mimetype="application/json")
    except ValueError:
        return func.HttpResponse("Invalid input", status_code=400)
    
@app.route(route="get-all-tasks", methods=["GET"])
def get_all_tasks(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request to get all tasks.')

    return func.HttpResponse(json.dumps(tasks), status_code=200, mimetype="application/json")
```

### Step 5: Test Your Azure Functions Locally and Deploy to Azure

You can test your Azure Functions locally by starting the Azure Functions host. Run the following command in your project directory:

```bash
func start
```

This will start the local Azure Functions runtime, and you will see URLs for each of your functions. You can use tools like `curl` or Postman to test the HTTP endpoints.

### Example Requests

**Add Task**

```bash
curl -X POST http://localhost:7071/api/add-task -H "Content-Type: application/json" -d '{"title": "New Task"}'
```

**Get Task by ID**

```bash
curl http://localhost:7071/api/get-task?id=1
```

**Update Task**

```bash
curl -X PUT http://localhost:7071/api/update-task?id=1 -H "Content-Type: application/json" -d '{"title": "Updated Task"}'
```

**Delete Task**

```bash
curl -X DELETE http://localhost:7071/api/delete-task?id=1
```

**Get All Tasks**

```bash
curl http://localhost:7071/api/get-all-tasks
```

You can publish the azure functions by running the following command:

```bash
func azure functionapp publish <FunctionAppName>
```

### Conclusion

In this tutorial, we have created a simple Python application that demonstrates CRUD functionality using Azure Functions. We also covered how to test these functions locally using Azure Functions Core Tools. With this setup, you can easily extend the functionality and deploy it to Azure for a scalable, serverless application.

Microsoft documentation on developing Azure functions: [https://learn.microsoft.com/en-us/azure/azure-functions/functions-run-local?tabs=linux%2Cisolated-process%2Cnode-v4%2Cpython-v2%2Chttp-trigger%2Ccontainer-apps&pivots=programming-language-python](https://learn.microsoft.com/en-us/azure/azure-functions/functions-run-local?tabs=linux%2Cisolated-process%2Cnode-v4%2Cpython-v2%2Chttp-trigger%2Ccontainer-apps&pivots=programming-language-python)