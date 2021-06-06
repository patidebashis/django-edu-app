## Introduction:

I have created a sample web application named "django-edu-app" which exposes a http endpoint. The application is deployed in AWS and uses Terraform for end to end automation.

## Tool Versions:

Django v3.1 
Docker v19.03.12
Python v3.8.5
Terraform v0.13.0

## I have tried to automate the  below:

Set up the following AWS infrastructure with the below:

- Networking:
    - VPC
    - Public and private subnets
    - Routing tables
    - Internet Gateway
    - Key Pairs
- Security Groups
- Load Balancers, Listeners, and Target Groups
- IAM Roles and Policies
- ECS:
    - Task Definition (with multiple containers)
    - Cluster
    - Service
- Launch Config and Auto Scaling Group
- RDS
- Health Checks and Logs


##Set up Instructions:


## Pre-requisites

1. Install Terraform

1. Sign up for an AWS account

1. Create two ECR repositories, `django-app` and `nginx`.

1. Fork/Clone

1. Build the Django and Nginx Docker images and push them up to ECR:

    ```sh
    $ cd app
    $ docker build -t <AWS_ACCOUNT_ID>.dkr.ecr.us-west-1.amazonaws.com/django-app:latest .
    $ docker push <AWS_ACCOUNT_ID>.dkr.ecr.us-west-1.amazonaws.com/django-app:latest
    $ cd ..
    $ cd nginx
    $ docker build -t <AWS_ACCOUNT_ID>.dkr.ecr.us-west-1.amazonaws.com/nginx:latest .
    $ docker push <AWS_ACCOUNT_ID>.dkr.ecr.us-west-1.amazonaws.com/nginx:latest
    $ cd ..
    ```

1. Update the variables in *terraform/variables.tf*.

1. Set the following environment variables, init Terraform, create the infrastructure:

    ```sh
    $ cd terraform
    $ export AWS_ACCESS_KEY_ID="YOUR_AWS_ACCESS_KEY_ID"
    $ export AWS_SECRET_ACCESS_KEY="YOUR_AWS_SECRET_ACCESS_KEY"

    $ terraform init
    $ terraform apply
    $ cd ..
    ```

1. Terraform will output an ALB domain. Create a CNAME record for this domain
   for the value in the `allowed_hosts` variable.

1. Open the EC2 instances overview page in AWS. Use `ssh ec2-user@<ip>` to
   connect to the instances until you find one for which `docker ps` contains
   the Django container. Run
   `docker exec -it <container ID> python manage.py migrate`.

1. Now you can open `https://your.domain.com/admin`. Note that `http://` may not  work.

1. You can also run the following script to bump the Task Definition and update the Service:

    ```sh
    $ cd deploy
    $ python update-ecs.py --cluster=production-cluster --service=production-service


Note: With the limited time I had, I cloud create this much only. I have neither used "AWS code pipeline" or "Fargate" so far. I have used jenkins extensively all these days but would love to explore code pipeline for future use.
