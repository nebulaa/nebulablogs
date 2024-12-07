---
title: "Essential Information on Container Scanning: A Complete Guide"
datePublished: Fri Dec 06 2024 21:52:27 GMT+0000 (Coordinated Universal Time)
cuid: cm4da7jcz000a09mn6v351qqz
slug: essential-information-on-container-scanning-a-complete-guide
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/in9-n0JwgZ0/upload/47576356972a6fb8f5e0aeb9330d6c2f.jpeg
tags: security, devops, devsecops, cybersecurity-1

---

Deploying applications as containers is becoming an increasingly popular approach for building, deploying, and managing software applications. The portability, scalability, and agility of containers make them an ideal solution for modern software development practices, such as continuous integration and continuous delivery (CI/CD).

In this document, we will go over the general concepts of containerization, how containers can introduce security vulnerabilities, and how to mitigate these risks.

## Key concepts

**Container:** A container is a standalone file or package of software files that include everything you need to run an application. Everything from the application’s code and dependencies, to its library, runtime, and system tools are all located within the container.

**Container Image:** A container image is a static file within a container that holds the code to run processes for your application. It can include system libraries, tools, and other settings needed to run on a containerized platform. These images are often built on a pre-existing parent image or base image in an OS to help developers avoid building lots of files from scratch.

**Container Registry:** A container registry is a repository that stores container images. It provides a centralized location for storing and sharing container images, making it easier to deploy and manage containerized applications.

**CI/CD:** Continuous Integration/Continuous Deployment (CI/CD) is a software development practice that involves automating the build, test, and deployment processes of software applications.

**Docker**: Docker is a popular containerization platform that allows developers to package applications into containers.

**Dockerfile**: A text file containing the configurations needed to build a Docker image.

**Container Hardening**: The process of enhancing security by reducing the attack surface of a containerized application.

**Container Scanning:** Container scanning refers to the systematic examination of container images to detect potential security threats such as known vulnerabilities, malware, and misconfigurations. It encompasses both static analysis (analyzing code without executing it) and dynamic analysis (testing how the software behaves when executed).

## Container Security Risks

The following list outlines the potential security risks associated with the use of containerized applications:

1. **Image Vulnerabilities**: The security of a container largely depends on the integrity of its base image. If an image contains vulnerabilities or is compromised, it can expose all containers built upon it to potential threats.
    
2. **Misconfiguration**: Incorrect configuration settings within container environments can lead to security gaps. This includes issues like running services with overly permissive permissions, exposing unnecessary ports, or failing to update and patch the underlying system regularly.
    
3. **Outdated Libraries and Frameworks**: Containers often include dependencies such as libraries and frameworks that may have known vulnerabilities or be outdated. If these dependencies are not updated regularly, they may introduce security risks.
    
4. **Data Leakage**: Containers share resources at a granular level, which means that if one application leaks secrets or sensitive data through its logs or network traffic, it can potentially affect other applications sharing the same container environment.
    
5. **Escalation of Privileges**: Without proper access controls, containers may allow processes to escalate their privileges beyond what is necessary for operation, creating security risks.
    

## Best Practices for Container hardening

The following are some best practices for container hardening that is recommended while developing the container images:

* Use docker containers with immutable tags for production environments. Rolling tags can be used for development environments.
    
* Use non-root user accounts for running containers.
    
* Sign container images to ensure integrity and authenticity. Docker engine can be configured to only run images signed by trusted parties.
    
* Protect the underlying linux kernel with AppArmor, Seccomp or SELinux security profiles.
    
* Enable resource limits for containers to prevent resource exhaustion attacks.
    
* Secure the container registry by implementing authentication and authorization mechanisms.
    
* Mount host-sensitive directories as read-only.
    
* Running ssh service within containers makes managing ssh keys/ access policies difficult. This should be avoided if possible. Instead, run ssh on the host and use `docker exec` or `docker attach` to interact with the container.
    
* Ensure that the container’s ports are not mapped to host ports below port 1024.
    
* Ports not necessary for the service must not be exposed.
    
* Avoid sharing host namespaces with containers.
    
* Restrict a container from acquiring new privileges by setting the `no_new_priv` bit in the kernel. Always run your docker images with `--security-opt=no-new-privileges` in order to prevent privilege escalation.
    

[Hadolint](https://github.com/hadolint/hadolint) is a Dockerfile linter that helps you build best practice Docker images.

## Types of Container Scanning

Containers are complex software systems that can be difficult to fully test and analyze without the use of a container scanning tool.

The analysis involves the scanner retrieving a container image from the container registry where it's stored. The scanner then decomposes the image into its constituent layers, which typically include the base image, application code, and dependencies. Decomposition is essential because it allows the scanner to examine each layer in isolation, identifying any areas where vulnerabilities might lurk.

### Image scanning

Image scanning examines the container images themselves, often before they are deployed. Here are three key concepts related to image scanning:

**Base image vulnerabilities**: Many containers are built from base images that can contain vulnerabilities. Container image scanning tools check these base images against known vulnerabilities in databases (like the CVE list) to ensure they don't have any outdated or compromised components.

**Application dependency checks**: Application dependency checks scan the libraries and packages the application within the container relies on. This type of scan also analyzes the software bill of materials (SBOM) to identify outdated libraries with known vulnerabilities.

**IaC scanning**: This process involves the examination of infrastructure as code files, such as Dockerfile, to validate configurations and detect potential misconfigurations or embedded secrets. IaC scanning helps ensure that the infrastructure provisioning scripts are secure and adhere to best practices, preventing the deployment of vulnerable containers.

### Runtime scanning

Runtime scanning (scanning that takes place while a container is active and running) has the following targets:

**System calls and processes**: Runtime scanners observe the system calls running containers make: If a container attempts to make an unexpected system call, it could indicate the presence of malicious activity.

**Anomalous behavior in real time**: This type of runtime scan looks for deviations from regular operation, which could suggest an intrusion. Anomalous behavior might include spikes in network traffic or unauthorized changes to files or configurations.

## Container Security Tools

1. [**BlackDuck**](https://www.blackduck.com/): A comprehensive container security solution that provides dependency analysis, binary analysis, license compliance and vulnerability management.
    
2. [**Trivy**](https://github.com/aquasecurity/trivy): A lightweight open-source tool for scanning containers for vulnerabilities, misconfigurations, and policy violations.
    
3. [**Clair**](https://github.com/quay/clair): Clair is an open source project for the static analysis of vulnerabilities in application containers (currently including OCI and docker).
    
4. [**Falco**](https://falco.org/): Falco is an open-source runtime security platform that allows you to detect and respond to suspicious behavior within Linux containers and applications.
    
5. [**Anchore**](https://anchore.com/opensource/): Anchore provides a developer-friendly vulnerability scanning tool for container image and also provides SBOM (Software Bill of Materials) generation support.
    
6. [**Dagda**](https://github.com/eliasgranderubio/dagda): A tool to perform static analysis of known vulnerabilities, trojans, viruses, malware & other malicious threats in docker images/containers and to monitor the docker daemon and running docker containers for detecting anomalous activities.
    
7. [**Hadolint**](https://github.com/hadolint/hadolint): A smart Dockerfile linter that helps you build best practice Docker images.
    
8. [**ggshield**](https://github.com/GitGuardian/ggshield): a CLI application that runs in the local environment or in a CI environment to help detect secrets, as well as other potential security vulnerabilities or policy breaks affecting the codebase.
    

## Implementation Steps

### Preparation

Define scanning policies, tools, and integration points with CI/CD pipelines.

* **Scanning Policy**: Define the scope of the scan, the type of scan, and the frequency of the scan.
    
* **Scanning Tool**: Choose the appropriate scanning tool based on the requirements of the organization and the type of container images being scanned.
    
* **Integration Points**: Establish the integration points with the CI/CD pipeline to trigger the scanning process.
    
    * Container scanning can be performed at different stages of the development process. For example, during the build phase when the container image is being built with the help of a Dockerfile.
        
    * The scanning tool can be integrated with the CI/CD pipeline to trigger the scan automatically when a new container image is pushed to or pulled from the container registry.
        
    * The recommended approach is to perform the scan before pushing the container image to a trusted registry.
        

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1733521632456/1d37eba9-221a-48ef-a490-d294c40dfd6c.png align="center")

### Configuration

Set up the scanning tool(s) according to organizational requirements and compliance standards. Does the scanning tool configuration check for:

* vulnerabilities in the base image used to build the container?
    
* use of outdated or vulnerable libraries and dependencies?
    
* misconfigurations in the infrastructure provisioning scripts?
    
* permissions and access control issues?
    
* leakage of sensitive data or credentials?
    
* security best practices and compliance with industry standards?
    

The recommendation is to use a combination of static and dynamic/runtime scanning tools to ensure comprehensive coverage of the container images.

### Execution and continuous monitoring

Container scanning needs to be performed at regular intervals to ensure that new vulnerabilities are identified and remediated promptly. The frequency of scanning depends on the organization's security policies and compliance requirements.

## Documentation and Reporting

The documentation of the container scan results should include the following information:

* The container image name and tag
    
* The date and time of the scan
    
* The scan tool used
    
* The scan results, including any vulnerabilities or misconfigurations found
    
* The remediation actions taken based on the scan results
    

The container scan reports should be reviewed regularly and the mitigation actions should be taken based on the severity and impact of the vulnerabilities identified.

The findings must be tracked and documented in a centralized system, such as a vulnerability management system, to ensure that they are worked on and remediated in a timely manner.