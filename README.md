# buildspec.yaml
```
version: 0.2

phases:
  pre_build:
    commands:
      - echo Logging in to Amazon ECR...
      - aws --version
      - aws ecr get-login-password --region [REGION] | docker login --username AWS --password-stdin [ACCOUNT-ID].dkr.ecr.ap-northeast-2.amazonaws.com
      - REPOSITORY_URI=[ECR-URL]
      - IMAGE_TAG=$(date "+%Y-%m-%d.%H.%M.%S")
  build:
    commands:
      - echo Build started on `date`
      - echo Building the Docker image...
      - docker build -t $REPOSITORY_URI:latest .
      - docker tag $REPOSITORY_URI:latest $REPOSITORY_URI:$IMAGE_TAG
  post_build:
    commands:
      - echo Build completed on `date`
      - echo Pushing the Docker images...
      - docker push $REPOSITORY_URI:$IMAGE_TAG
      - echo Writing image definition file...
      - echo "$(jq .containerDefinitions[].image=\"$REPOSITORY_URI:$IMAGE_TAG\" taskdef.json)" > taskdef.json
artifacts:
  files:
    - 'appspec.yml'
    - 'taskdef.json'
```
# appspec.yaml
```
version: 0.0

Resources:
  - TargetService:
      Type: AWS::ECS::Service
      Properties:
        TaskDefinition: "<TASK_DEFINITION>"
        LoadBalancerInfo:
          ContainerName: "[NAME]"
          ContainerPort: [PORT]
```
