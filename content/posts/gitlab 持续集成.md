---
title: gitlab 持续集成
date: 2018-02-05 11:35:00
categories: ["其他"]
---


# 安装gitlab-runner

https://docs.gitlab.com/runner/install/index.html

国内镜像地址

https://mirrors.tuna.tsinghua.edu.cn/help/gitlab-ci-multi-runner/

# 注册Runner

https://docs.gitlab.com/runner/register/

#### 步骤

注意：用户为root用户

```
root@sw:/home/sw# gitlab-runner register xuqi
Running in system-mode.

Please enter the gitlab-ci coordinator URL (e.g. https://gitlab.com/):
http://10.168.2.114/

Please enter the gitlab-ci token for this runner:

输入gitlab的token

Please enter the gitlab-ci description for this runner:
[sw]: xuqi
Please enter the gitlab-ci tags for this runner (comma separated):

Whether to lock the Runner to current project [true/false]:
[true]: true
Registering runner... succeeded                     runner=wzb81TcG
Please enter the executor: docker, parallels, shell, virtualbox, docker+machine, kubernetes, docker-ssh, ssh, docker-ssh+machine:
shell
Runner registered successfully. Feel free to start it, but if it's running already the config should be automatically reloaded!
```

注意： token位置在http://localhost/xxx/xxx/settings/ci_cd 页面里的Runners settings里


#### 如何删除在gitlab中不再使用的runner

> gitlab-runner verify --delete

## 关于executors的选择

https://docs.gitlab.com/runner/executors/README.html

# 如何编写.gitlab-ci.yml

### 官方文档

https://gitlab.com/help/ci/yaml/README.md

#### stages
> stages用来定义可以被job调用的stages。stages的规范允许有灵活的多级pipelines。

#### Jobs
> .gitlab-ci.yml允许指定无限量jobs。每个jobs必须有一个唯一的名字，而且不能是上面提到的关键字。job由一列参数来定义jobs的行为

### demo

```
image: python:3.6.1

before_script:
   - export BIZDATE=`date +%Y%m%d`
   - export PROJECT_NAME=$CI_PROJECT_NAME
   - export VERSION=$CI_COMMIT_TAG-$CI_COMMIT_SHA

stages:
  - build
  - package
  - deploy

build:
  stage: build
  script: 
    - /home/gitlab-runner/anaconda3/bin/python3.6 -O -m compileall .
    - find . -name '*.pyc' -exec rename 's/.cpython-36.opt-1//' {} \;
    - find . -name '*.pyc' -execdir mv {} .. \;
    - find . -name '*.py' -type f -print -exec rm {} \;
    - find . -name '__pycache__' -exec rm -rf {} \;
    
package:
  stage: package
  script:
    - rm -rf .git
    - rm -rf .gitlab-ci.yml
    - $(tar -zcf ../deploy-$VERSION.tar.gz .)
    - $(mkdir -p /home/gitlab-runner/backup/$PROJECT_NAME/$BIZDATE)
    - $(cp ../deploy-$VERSION.tar.gz /home/gitlab-runner/backup/$PROJECT_NAME/$BIZDATE/)
    - $(rm -rf ../deploy-$VERSION.tar.gz)
    - rm -rf .

deploy:
  stage: deploy
  script:
    - echo 'deploy'

```

服务器版本，使用脚本编写如上内容

```
before_script:
   - export PROJECT_NAME=$CI_PROJECT_NAME
   - export VERSION=$CI_COMMIT_TAG-$CI_COMMIT_SHA
   - export WORK_DIR=`pwd`
   - export HOST=''

stages:
  - deploy

deploy:
  stage: deploy
  only:
    - /^release-.*$/
  script:
    - ~/script/deploy.sh $WORK_DIR $HOST
```

deploy.sh 脚本内容

```
workdir=$1
BIZDATE=`date +%Y%m%d`
echo $workdir
cd $workdir
rm -rf .git
rm -rf .gitlab-ci.yml
tar -zcf ../bak-$VERSION.tar.gz .
mkdir -p /home/gitlab-runner/backup/$PROJECT_NAME/$BIZDATE
cp ../bak-$VERSION.tar.gz /home/gitlab-runner/backup/$PROJECT_NAME/$BIZDATE/
rm -rf ../bak-$VERSION.tar.gz
/home/gitlab-runner/anaconda3/bin/python3.6 -O -m compileall .
find . -name '*.pyc' -exec rename 's/.cpython-36.opt-1//' {} \;
find . -name '*.pyc' -execdir mv {} .. \;
find . -name '*.py' -type f -print -exec rm {} \;
find . -name '__pycache__' -exec rm -rf {} \;
#rm -rf $1/*

tar -zcf ../deploy-$VERSION.tar.gz .
for line in `cat hosts`
do
   pem=`echo $line | cut -d \: -f 1`
   host=`echo $line | cut -d \: -f 2`
   echo $pem
   echo $host
   scp -i ~/keys/$pem ../deploy-$VERSION.tar.gz ubuntu@$host:/home/ubuntu/
done
echo 'finish'
```

# 参考

https://scarletsky.github.io/2016/07/29/use-gitlab-ci-for-continuous-integration/

https://segmentfault.com/a/1190000010442764






