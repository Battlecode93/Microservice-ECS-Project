# AWS Microservice ECS Project

![Alt text](Microservice-ECS.png)
----

This repository documents the deployment of a web application using Amazon ECS with EC2, Docker, ECR, Fargate, and Load Balancer. The project involves setting up an EC2 instance, building and running a Docker image, pushing the image to ECR, and deploying the application using ECS with a load balancer for high availability.

## Project Overview

This project demonstrates how to deploy a web application on AWS using:
- Amazon EC2
- Docker
- Amazon ECR
- Amazon ECS Fargate
- Application Load Balancer

## Architecture

The architecture of this project involves the following steps:
1. Launch an EC2 Instance and install Docker.
2. Build a Docker image and run it on the EC2 instance.
3. Create an Amazon ECR repository, tag the Docker image, and push it to ECR.
4. Create an Application Load Balancer to balance traffic across tasks.
5. Create an ECS Task Definition and a Cluster.
6. Deploy the application using ECS Fargate and the Load Balancer.

## Setup and Deployment Process

### 1. Launch EC2 Instance and Install Docker

- SSH into the EC2 instance.
- Install Docker using the following commands:
  ```sh
  curl -fsSL https://get.docker.com -o get-docker.sh
  sudo sh get-docker.sh
  sudo systemctl start docker
  sudo systemctl enable docker
  ```

### 2. Build Docker Image and Run It

- Create a project directory:
  ```sh
  mkdir myproject
  cd myproject
  ```
- Create a `Dockerfile`:
  ```sh
  vim Dockerfile
  ```
  Example content for `Dockerfile`:
  ```Dockerfile
  FROM nginx:latest
  COPY index.html /usr/share/nginx/html/index.html
  ```
- Create an `index.html` file:
  ```sh
  vim index.html
  ```
  Example content for `index.html`:
  ```html
  <html>
  <body>
    <h1>Welcome to My Project</h1>
  </body>
  </html>
  ```
- Build the Docker image:
  ```sh
  docker build -t project-image .
  ```
- Run the Docker container:
  ```sh
  docker run -d -p 80:80 --name project-container project-image
  ```
- Verify the application is running by accessing the public IP address of the EC2 instance.

### 3. Create an ECR Repository and Push Image

- Create a private repository in ECR named `myecr-repo`.
- Create a role in IAM with necessary permissions and attach it.
- Configure AWS CLI with IAM user credentials.
- Log in to ECR:
  ```sh
  aws ecr get-login-password --region your-region | docker login --username AWS --password-stdin your-account-id.dkr.ecr.your-region.amazonaws.com
  ```
- Tag the Docker image:
  ```sh
  docker tag project-image:latest your-account-id.dkr.ecr.your-region.amazonaws.com/myecr-repo:latest
  ```
- Push the image to ECR:
  ```sh
  docker push your-account-id.dkr.ecr.your-region.amazonaws.com/myecr-repo:latest
  ```

### 4. Create a Load Balancer

- Create an Application Load Balancer named `project-alb` with three availability zones.
- Use the same security group as the ECS instance.
- Create a target group and adjust the health check settings.

### 5. Create ECS Task Definition and Cluster

- Create a task definition using the Fargate launch type.
  - Specify the container definition with the image URI from ECR.
- Create an ECS cluster.

### 6. Deploy Application

- Create a service within the ECS cluster.
- Attach the load balancer to the service.
- Use the DNS name of the Application Load Balancer to access the application, ensuring high availability.

## Summary

This project demonstrates how to deploy a web application on AWS using a combination of EC2, Docker, ECR, ECS Fargate, and an Application Load Balancer. By following the steps outlined above, you can achieve a scalable and highly available deployment for your application.

