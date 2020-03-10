env:
  secrets-manager:
    key: secret-id:json-key:version-stage:version-id
phases:
  install:
    runtime-versions:
      python: 3.8
  pre_build:
    commands:
      - curl https://stedolan.github.io/jq/download/linux64/jq 
      - chmod +x ./jq
  build:
    commands:
      - curl 169.254.170.2$AWS_CONTAINER_CREDENTIALS_RELATIVE_URI > cred.json
      - aws configure set aws_access_key_id
      - aws configure set aws_secret_access_key
      - aws configure set aws_session_token
      - aws configure set region $AWS_REGION





      - echo Create a VM with incorrect set of tags
      - aws ec2 run-instances --image-id ami-014b57929052d0007 --count 1 --instance-type t2.micro --key-name MyKeyPair.pem --security-group-ids sg-72223e1a --subnet-id subnet-cd1476a5 --tag-specifications 'ResourceType=instance,Tags=[{Key=Name,Value=SecurityTest},{Key=CostCenter,Value=6434},{Key=ApplicationID,Value=AID390}]' 'ResourceType=volume,Tags=[{Key=Name,Value=SeccurityTest},{Key=Owner,Value=xh575},{Key=Creator,Value=xh575},{Key=CostCenter,Value=6434},{Key=Product,Value=Infrastructure},{Key=ApplicationID,Value=AID390}]'> instance2.json      
      - aws ec2 describe-instances | jq '.Reservations[].Instances[] | select(contains({Tags: [{Key: "YOUR_KEY_NAME_HERE"} ]}) | not)' 
      - aws ec2 describe-instances --query 'Reservations[].Instances[?!not_null(Tags[?Key == `Name`].Value)] | []'