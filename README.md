# ECS Docker Web Application

AWS ECS to create a sample Docker image for running a web application

## Prerequisites

- AWS CLI configured
- Docker installed
- AWS ECR repository created
- ECS cluster created

## Project Structure:
```
CN_DevOps_Project/
├── app/
│   └── index.html
├── Dockerfile
├── ecs-task-definition.json
└── README.md
```
## Prerequisite
- AWS CLI
- DOCKER ENGINE/DESKTOP
- Create user >> add to admin group >> apply full-admin-access policy >> security >> create access keys
- install awscli >> aws --version >> aws configure
- enter access key, secret access key, region-code, output-json 

## Steps

1. **Clone the repository:**
```
git clone https://github.com/y-runwal/CN_DevOps_Project.git
cd CN_DevOps_Project
```
2. **Build Docker Image:**
```
docker build -t CN_DevOps_Project .
# Example with a specific tag:
sudo docker build -t yrunwal/CN_DevOps_Project .

```

3. **Push to ECR:**
Create public repo | name-yuvrajrunwal >> view push commands
```
aws ecr get-login-password --region <YOUR_REGION> | docker login --username AWS --password-stdin <YOUR_ECR_URL>
docker tag ecs-docker-webapp:latest <YOUR_ECR_URL>:latest
docker push <YOUR_ECR_URL>:latest
example:
aws ecr get-login-password --region <YOUR_REGION> | docker login --username AWS --password-stdin <YOUR_ECR_URL>
docker tag CN_DevOps_Project:latest <YOUR_ECR_URL>:latest
docker push <YOUR_ECR_URL>:latest

```
4. **Create ECS Cluster:**
Navigate to ECS in the AWS Management Console and create a new ECS cluster (Fargate).

5. **Register ECS Task Definition:**
Use the ecs-task-definition.json to register a new task definition in ECS.

Create ECS Cluster | cluster name - mycluster
IAM - Create Role - Elastic Container Service(Allows ECS to create and manage AWS resources on your behalf) | Name - ECSRole
IAM - Create Role - Elastic Container Service Task (Allows ECS tasks to call AWS services on your behalf) | Name - ECSTaskRole

note down ECSRole, ECSServiceRole ARN, Image URL to update in JSON file
examples:
- arn:aws:iam::021891610508:role/ECSRole
- arn:aws:iam::021891610508:role/aws-service-role/ecs.amazonaws.com/AWSServiceRoleForECS
- 112609445401.dkr.ecr.ap-south-1.amazonaws.com/yuvraj_mit/cndevops_project
```
{
  "family": "ecs-docker-webapp",
  "networkMode": "awsvpc",
  "containerDefinitions": [
    {
      "name": "webapp-container",
      "image": "<YOUR_ECR_URL>:latest",
      "essential": true,
      "portMappings": [
        {
          "containerPort": 80,
          "hostPort": 80,
          "protocol": "tcp"
        }
      ],
      "memory": 512,
      "cpu": 256
    }
  ],
  "requiresCompatibilities": [
    "FARGATE"
  ],
  "cpu": "256",
  "memory": "512",
  "executionRoleArn": "arn:aws:iam::021891610508:role/ECSRole",
  "taskRoleArn": "arn:aws:iam::021891610508:role/aws-service-role/ecs.amazonaws.com/AWSServiceRoleForECS"
}
```
Task Definition >> Create New Definition
Deploy >> Create Service 

7. **Deploy to ECS:**
Create a service in your ECS cluster, link it to the task definition, and deploy the web application.
Expose the service via a load balancer to access the web app

8. **Access the Web Application:**
After the service is running, access the application via the Load Balancer’s DNS.
The application is a simple HTML page served by an Nginx web server.

## Troubleshoot
ERROR: Cannot connect to the Docker daemon.... Is the docker daemon running?
Solution: Make sure docker engine/docker desktop is in running state.
