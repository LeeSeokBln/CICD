# buildspec.yaml
```
version: 0.2

phases:
  pre_build:
    commands:
      - <commands>
  build:
    commands:
      - <commands>
  post_build:
    commands:
      - <commands>
artifacts:
  files:
    - 'appspec.yml'
```
## 버전명시가 필요할 시
```
  install:
    runtime-versions:
      <language>: <version>  #EX) python: 3.8
```
# appspec.yaml
```
version: 0.0
os: linux
files:
  - source: /
    destination: /var/www/html/WordPress
hooks:
  BeforeInstall:
    - location: scripts/install_dependencies.sh
      timeout: 180
      runas: root
  AfterInstall:
    - location: scripts/change_permissions.sh
      timeout: 180
      runas: root
  ApplicationStart:
    - location: scripts/start_server.sh
    - location: scripts/create_test_db.sh
      timeout: 180
      runas: root
  ApplicationStop:
    - location: scripts/stop_server.sh
      timeout: 180
      runas: root

```
# 인스턴스에 SSM 수동 설치 (amazon linux2)
```
sudo yum install -y https://s3.amazonaws.com/ec2-downloads-windows/SSMAgent/latest/linux_amd64/amazon-ssm-agent.rpm
sudo systemctl start amazon-ssm-agent
```
