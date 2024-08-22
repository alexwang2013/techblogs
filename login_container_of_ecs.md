# how to login to contianer of AWS ECS
This docoment tells you how to login to contianer of AWS ECS
## enable enable-execute-command for you service

```
aws ecs update-service --cluster <Cluster> --service <Service> --enable-execute-command　--region <Region>
```

e.g.
```
aws ecs update-service --cluster poc-cluster --service study-ecs-fluentbit-firehose-service --enable-execute-command --region us-east-1
```

## allow the ecs execution role to access to session manager
add the following policy to the ecs execution role
```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "ssm:StartSession",
                "ssm:SendCommand",
                "ssm:DescribeInstanceInformation",
                "ssm:DescribeSessions",
                "ssm:GetCommandInvocation",
                "ssmmessages:CreateControlChannel",
                "ssmmessages:CreateDataChannel",
                "ssmmessages:OpenControlChannel",
                "ssmmessages:OpenDataChannel"
            ],
            "Resource": "*"
        }
    ]
}
```

## restart the ecs task


## login contianer of AWS ECS
execute the following command to login contianer of AWS ECS on your EC2 instance or your local computer.
```
aws ecs execute-command --cluster <Cluster> \
    --task <taskId> \
    --container <ContainerName> \
    --interactive \
    --region <Region> \
    --command "/bin/sh" 
```

e.g.
```
aws ecs execute-command --cluster poc-cluster \
    --task 4b675a8f10f644cbab02e42744eaac97 \
    --container log_router \
    --interactive \
    --region us-east-1 \
    --command "/bin/sh" 
```

## run task without service in ecs cluster
if there is no service in the cluster, we can run task with like the following.

```
aws ecs run-task \
    --cluster poc-cluster \
    --task-definition ecs-fluentbit-firehose \
    --launch-type FARGATE \
    --enable-execute-command \
    --region us-east-1 \
    --network-configuration '{"awsvpcConfiguration": {"subnets": ["subnet-12345577f"], "assignPublicIp": "ENABLED"}}'
```