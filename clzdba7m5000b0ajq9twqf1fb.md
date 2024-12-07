---
title: "Deploying DefectDojo on Azure Cloud with Secure PostgreSQL Integration - Complete Walkthrough"
datePublished: Fri Aug 02 2024 23:04:00 GMT+0000 (Coordinated Universal Time)
cuid: clzdba7m5000b0ajq9twqf1fb
slug: deploying-defectdojo-on-azure-cloud-with-secure-postgresql-integration-complete-walkthrough
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/CG7YwmRDGvI/upload/668296c781f8310965f023f92009baf1.jpeg
tags: azure, security, devops, devsecops, cybersecurity-1

---

DefectDojo is an open-source platform designed for DevSecOps, ASPM (Application Security Process Management), vulnerability management, and more.

This guide provides step-by-step instructions to set up and configure DefectDojo on an Azure Virtual Machine (VM) with a secure PostgreSQL database. We'll create necessary network configurations and ensure secure access with SSL/TLS.

### Step 1: Create an Azure Virtual Machine

1. **Create the VM**:
    
    * Open the Azure portal and go to "Virtual machines".
        
    * Click "Create" and choose "Azure virtual machine".
        
    * Configure the following settings:
        
        * **Image**: Ubuntu Server: Linux (ubuntu 24.04)
            
        * **Size**: Standard B2s (2 vCPUs, 4GB RAM)
            
        * **Storage**: 30GB SSD
            
        * **DNS Name**: Set it to something like [defect-dojo-nebula.eastus.cloudapp.azure.com](http://defect-dojo-nebula.eastus.cloudapp.azure.com).
            
            ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1722634909235/8defbab1-a8f4-4a5b-ae0f-b971b610acde.png align="center")
            
2. **Networking Configuration**:
    
    * Allow inbound ports 80 (HTTP) and 443 (HTTPS).
        
    * Allow your IP address for SSH (port 22).
        
        ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1722635189498/d9902525-d804-4931-81df-0d83075225ef.png align="center")
        
3. **Provision the VM** and connect via SSH.
    
    To connect to the VM using the private SSH key:
    
    In your local machine:
    
    ```bash
    cp defect-dojo_key.pem ~/.ssh/
    cd ~/.ssh/
    chmod 400 defect-dojo_key.pem
    ssh -i defect-dojo_key.pem azureuser@defect-dojo-nebula.eastus.cloudapp.azure.com
    ```
    
    ### Step 2: Create Azure PostgreSQL Database with VNET Integration and peer VNETs
    
    1. **Create the PostgreSQL Database**:
        
        * Navigate to "Azure Database for PostgreSQL" and click "Create".
            
        * Select "Single Server" and configure the basic settings.
            
        * Disable public access.
            
            ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1722635963318/c659d71b-69a2-4f65-8cf4-adacc952aa65.png align="center")
            
            ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1722636040141/89eed88b-edf8-45e6-9afb-5783d7188469.png align="center")
            
            ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1722635826338/1f8ac159-be3e-4041-b540-be675b602750.png align="center")
            
    2. **Configure VNET Integration**:
        
        * Enable VNET integration for the PostgreSQL server.
            
        * This automatically creates a private DNS zone, e.g., [defect-dojo-db-test.private.postgres.database.azure.com](http://defect-dojo-db-test.private.postgres.database.azure.com)
            
    3. **Peer VNETs**:
        
        * In the Azure portal, search for the VNET of Azure Virtual Machine and select "Peerings".
            
            ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1722636251660/6eb9f189-6e87-44a4-94c6-8c3ebb74265f.png align="center")
            
        * Create a peering connection to VNET of the Database.
            
            ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1722636324804/754d574d-e891-4568-adb9-7f5bbcf62fc0.png align="center")
            
    4. **Update Private DNS Zone**:
        
        * In the private DNS zone, select `Virtual Network Links`.
            
        * Add both VNETs with auto-registration enabled.
            
            ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1722636411925/24fda172-cb73-4e24-9909-095bc26e18fe.png align="center")
            
            ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1722636451702/659272ba-ff9c-4d72-9439-bd5acc25abba.png align="center")
            

### Step 3: Configure the Azure VM

1. **Install Required Software**:
    
    ```bash
    sudo apt-get update
    sudo apt-get install -y postgresql-client certbot
    ```
    
    Install docker based on instructions here: [https://docs.docker.com/engine/install/ubuntu/](https://docs.docker.com/engine/install/ubuntu/)
    
2. **Check PostgreSQL Connection:**
    
    Find the Database connection details under `Connect` section:
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1722636692619/1e0c4e8f-4e47-4d34-92a4-93ee312b78af.png align="center")
    
    ```bash
    psql -h defect-dojo-db-test.postgres.database.azure.com -p 5432 -U test_admin postgres
    ```
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1722636780531/1edad5a1-f792-4ae0-bf67-4870fd6fc6b2.png align="center")

### Step 4: Set Up SSL Certificates

1. **Generate SSL Certificates**:
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1722636882831/db99633d-21c8-494c-b451-6edd6a1b7335.png align="center")
    
    ```bash
    certbot certonly
    # Select option 1 and follow the prompts to generate .pem files.
    ```
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1722636915171/8d2f0ee9-af09-4e5d-aeca-fd9bd46d27fe.png align="center")
    
    Certificate is saved at: /etc/letsencrypt/live/defect-dojo-nebula.eastus.cloudapp.azure.com/fullchain.pem Key is saved at: /etc/letsencrypt/live/defect-dojo-nebula.eastus.cloudapp.azure.com/privkey.pem
    

### Step 5: Deploy DefectDojo

1. **Clone the DefectDojo Repository**:
    
    ```bash
    git clone https://github.com/DefectDojo/django-DefectDojo.git
    cd django-DefectDojo
    ```
    
2. **Move SSL Certificates**:
    
    ```bash
    cp /etc/letsencrypt/live/defect-dojo-nebula.eastus.cloudapp.azure.com/fullchain.pem nginx/
    cp /etc/letsencrypt/live/defect-dojo-nebula.eastus.cloudapp.azure.com/privkey.pem nginx/
    chmod 400 nginx/privkey.pem
    ```
    
3. **Configure Docker**:
    
    ```bash
    rm -f docker-compose.override.yml
    ln -s docker-compose.override.https.yml docker-compose.override.yml
    ```
    
4. **Edit Configuration Files**:
    
    * **docker-compose.override.https.yml**:
        
        ```yaml
        ---
        services:
          nginx:
            environment:
              USE_TLS: 'true'
              GENERATE_TLS_CERTIFICATE: 'false'
            ports:
              - target: 8443
                published: 443
                protocol: tcp
                mode: host
          uwsgi:
            environment:
              DD_SESSION_COOKIE_SECURE: 'True'
              DD_CSRF_COOKIE_SECURE: 'True'
        ```
        
    * **Dockerfile.nginx-alpine** and **Dockerfile.nginx-debian**:
        
        ```dockerfile
        COPY wsgi_params nginx/nginx.conf nginx/nginx_TLS.conf /etc/nginx/
        COPY nginx/fullchain.pem nginx/privkey.pem /etc/nginx/ssl/
        RUN \
          chmod 400 /etc/nginx/ssl/privkey.pem
        ```
        
    * **docker/environments/postgres-redis.env**:
        
        ```bash
        ## Change the below variables based on your Azure Postgres DB Values.
        DD_DATABASE_URL=postgresql://test_admin:NotMyPassword404@defect-dojo-db-test.postgres.database.azure.com:5432/postgres
        DD_DATABASE_ENGINE=django.db.backends.postgresql
        DD_DATABASE_HOST=postgres
        DD_DATABASE_PORT=5432
        
        DD_DATABASE_NAME=postgres
        DD_DATABASE_USER=test_admin
        DD_DATABASE_PASSWORD=NotMyPassword404
        
        DD_TEST_DATABASE_NAME=test_defectdojo
        DD_TEST_DATABASE_URL=postgresql://defectdojo:defectdojo@postgres:5432/test_defectdojo
        
        DD_CELERY_BROKER_URL=redis://redis:6379/0
        
        DD_DOCKERCOMPOSE_DATABASE=postgres
        DD_DOCKERCOMPOSE_BROKER=redis
        ```
        
    * **nginx/nginx\_TLS.conf**:
        
        ```python
        #change from here
        server {
            listen 8080;
            location / {
                return 301 https://your-azure-vm-dns-name.cloudapp.azure.com/; # changed
            }
        }
        # Disable metrics auth for localhost (for nginx prometheus exporter)
        geo $metrics_auth_bypass {
          127.0.0.1/32 "off";
          default "Metrics";
        }
        server {
          server_tokens off;
          listen 8443 ssl;
          server_name your-azure-vm-dns-name.cloudapp.azure.com; # changed
          ssl_certificate /etc/nginx/ssl/fullchain.pem; # changed
          ssl_certificate_key /etc/nginx/ssl/privkey.pem; # changed
        ```
        

### Step 6: Build and Run DefectDojo

1. **Build Docker Images**:
    
    ```bash
    # Start Docker
    systemctl start docker
    # Build Images
    ./dc-build.sh
    ```
    
2. **Run Docker Containers**:
    
    ```bash
    ./dc-up.sh postgres-redis
    ```
    
    Recommendation is to use postgres-redis profile since support for MySQL and RabbitMQ will be deprecated by Defect Dojo team.
    
3. **Retrieve Admin Password**:
    
    ```bash
    docker-compose logs initializer | grep "Admin password:"
    ```
    
4. **Visit the Site**:
    
    * Open your browser and go to [`https://{your-azure-VM-DNS-name/`](https://defect-dojo.uswest.cloudapp.azure.com/).
        
    * Log in with the admin credentials.
        

### Step 7: Run Docker Containers in Detached Mode

1. **Stop Running Containers**:
    
    ```python
    ./dc-stop.sh
    ```
    
2. **Remove Docker Containers**:
    
    ```python
    ./dc-down.sh
    ```
    
3. **Rebuild Docker Images**:
    
    ```python
    ./dc-build.sh
    ```
    
4. **Run Containers in Detached Mode**:
    
    ```python
    ./dc-up-d.sh postgres-redis
    ```
    

### Conclusion

Deploying DefectDojo on Azure provides a robust, scalable solution for DevSecOps teams looking to manage vulnerabilities effectively while leveraging cloud services. This guide outlined the essential steps from setting up virtual networks to configuring Docker containers with SSL encryption, ensuring your application is both secure and accessible within an Azure environment.

Check out DefectDojo documentation here:

[https://documentation.defectdojo.com/](https://documentation.defectdojo.com/)

[https://github.com/DefectDojo/django-DefectDojo/blob/master/readme-docs/DOCKER.md](https://github.com/DefectDojo/django-DefectDojo/blob/master/readme-docs/DOCKER.md)