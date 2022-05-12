# Factorio on AWS

An AWS hosted Factorio game server for about $0.10/hr (USD)

## Storage

Encrypted EFS, this means game files persist even when the server turns off.

## Game Server

Runs on AWS ECS Fargate Spot instances using ephemeral storage.

## Usage

### Deploy

```shell
aws cloudformation deploy \
  --stack-name factorio \
  --capabilities CAPABILITY_IAM \
  --template-file ./template.yaml
```

### Start

```shell
aws ecs update-service --cluster factorio --service factorio --desired-count 1
```

### Stop

```shell
aws ecs update-service --cluster factorio --service factorio --desired-count 0
```

### Get IP

This triple-nested monstrosity will get what you need

```shell
aws ec2 describe-network-interfaces --network-interface-ids $( aws ecs describe-tasks --cluster pwmcintyre-factorio --tasks $( aws ecs list-tasks --cluster pwmcintyre-factorio --query 'taskArns[0]' --output text ) --query 'tasks[0].attachments[0].details[?name == `networkInterfaceId`].value | [0]'  --output text ) --query 'NetworkInterfaces[0].Association.PublicIp' --output text
```

## Pricing

### Host

https://aws.amazon.com/fargate/pricing/

- per vCPU per hour	$0.04856
  - 2 CPU (2048) = 2 * $0.04856 = $0.09712

*be sure to scale-down when not in use!*

### Storage

https://aws.amazon.com/efs/pricing/

$0.047 per 1GB per month (unlikely to excede 1 GB)

## Contribution

I love PR's

## References

- https://github.com/m-chandler/factorio-spot-pricing
