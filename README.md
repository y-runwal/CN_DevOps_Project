# ECS Docker Web Application

AWS ECS to create a sample Docker image for running a web application

## Prerequisites

- AWS CLI configured
- Docker installed
- AWS ECR repository created
- ECS cluster created

## Project Structure:
```
ecs-docker-webapp/
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
git clone https://github.com/atulkamble/ecs-docker-webapp.git
cd ecs-docker-webapp
```
2. **Build Docker Image:**
```
docker build -t ecs-docker-webapp .
example: sudo docker build -t atuljkamble/ecs-docker-webapp .
```

3. **Push to ECR:**
Create public repo | name-atulkamble >> view push commands
```
aws ecr get-login-password --region <YOUR_REGION> | docker login --username AWS --password-stdin <YOUR_ECR_URL>
docker tag ecs-docker-webapp:latest <YOUR_ECR_URL>:latest
docker push <YOUR_ECR_URL>:latest
example:
aws ecr-public get-login-password --region us-east-1 | docker login --username AWS --password-stdin public.ecr.aws/t2r6c7e4
// login succeeded
docker build -t atulkamble .
docker tag atulkamble:latest public.ecr.aws/t2r6c7e4/atulkamble:latest
docker push public.ecr.aws/t2r6c7e4/atulkamble:latest
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
- public.ecr.aws/t2r6c7e4/atulkamble:latest

```
{
  "family": "ecs-docker-webapp",
  "networkMode": "awsvpc",
  "containerDefinitions": [
    {
      "name": "webapp-container",
      "image": "public.ecr.aws/t2r6c7e4/atulkamble:latest",
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
ERROR: Cannot connect to the Docker daemon at unix:///Users/atul/.docker/run/docker.sock. Is the docker daemon running?
Solution: Make sure docker engine/docker desktop is in running state.
