# DevOps Tech Challange

## Deployed solution (running on my AWS stack for a few days)

(will be switched off soon, please wait / reload page in 20-25secs to see the data, because of serverless Aurora warmup)

http://techc-loadb-ab6i68w9vbng-1211233752.eu-west-1.elb.amazonaws.com/

## High level architectural overview

The deployment creates first a VPC network with separated private and public subnets distributed in 2 availbility zones to provide high availability. The private subnet resources cannot reach the Internet.
A serverless AWS Aurora PostgreSQL cluster has deployed with AutoScaling capability to the private subnets. The secrets generated and stored in SSM.
An AWS ECS cluster deployed to public multi-AZ subnets with serverless FARGATE task to create the initial database schema to PostgreSQL, this invoked by an AWS Lambda function.
Finally, an AutoScaled  ECS serverless FARGATE service deployed behind a V2 LoadBalancer to the ECS cluster running the Docker image URI given as the stack parameter. 
The above cost-effective and highly available solution does not use VMs and very flexible to scale. 
To improve security, the next step would be register S3 domain and offload HTTPS trffic with V2 LoadBalancer in front of the app service. The ECS tasks are deployed to public subnet to be able to get the public Docker image, from security viewpoint it would be a slight security enhancement to deploy them to private and adding a NAT gateway. 

[![N|Solid](https://raw.githubusercontent.com/tatobi/techchallange/master/docs/techchallange.png)](https://raw.githubusercontent.com/tatobi/techchallange/master/docs/techchallange.png)


## Prerequisites

- AWS account with administrator rights (the CloudFormation creates IAM roles)

- aws cli


## Process instructions

- git clone

- login to AWS WEB console /or/ login with aws cli

- use ./cloudformation/techchallange.yaml to create stack on UI OR use aws cli
 
- using aws cli tool to create stack (default input parameter is the servian's Docker image):

```
aws cloudformation create-stack \
    --stack-name "techchallange-01" \
    --template-body file://./cloudformation/techchallange.yaml \
    --capabilities CAPABILITY_IAM CAPABILITY_NAMED_IAM
```

- check stack status:

```
aws cloudformation describe-stack-events \
    --stack-name techchallange-01 \
    --output table

aws cloudformation describe-stacks \
    --stack-name techchallange-01 \
    --output table | grep StackStatus

```

- get output URL of stack:

```
aws cloudformation describe-stacks \
    --stack-name techchallange-01 \
    --output table | grep OutputValue

```
__NOTE__: please to wait / reload page to see the data on the URL because the Serverless Aurora cold-start needs warmup. This parameter can be changed.


- delete stack:

```
aws cloudformation delete-stack --stack-name techchallange-01
```

















