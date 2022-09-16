### 개요

- 개인 프로젝트시 간단하게 파이프라인 구성을 참고할 수 있도록 설정 내용을 정리했습니다.
- Jenkins의 Blue Ocean을 이용하여 파이프라인을 생성할 때 필요한 설정입니다.
- Elastic Beanstalk은 기본적으로 5000 포트를 사용하도록 프록시를 구성하기 때문에 배포하려는 프로그램이 5000포트를 사용하도록 설정합니다.

### 실행 환경

- Jenkins가 설치된 EC2 인스턴스
- 환경 구성이 완료된 Elastic Beanstalk
- 단일 spring boot 프로젝트가 배포된 Github Repository

### AMI 역할 생성 및 지정

- Jenkins가 설치된 인스턴스가 aws cli를 통해 s3와 beanstalk를 사용할 수 있는 권한이 필요하기 때문에 역할이 필요합니다.
- AmazonS3FullAccess, AdministratorAccess-AWSElasticBeanstalk 두 개의 정책을 가지고 있는 역할을 생성하고 지정해줍니다.

### Jar 파일을 업로드 할 S3 버킷 생성

- 버킷을 생성할 때 다음 설정을 제외하고 모두 차단 활성화를 해줍니다.
    - `새 ACL(액세스 제어 목록)을 통해 부여된 버킷 및 객체에 대한 퍼블릭 액세스 차단`

### Blue Ocean에서 파이프라인 생성에 필요한 Github Token

- repo, user:email 권한이 있는 토큰이 필요합니다.
- Blue Ocean에서 GUI로 파이프라인 생성을 하면 자동으로 커밋하는데 해당 동작시 필요한 듯 보입니다.

### Jenkinsfile

```
pipeline {
  agent any
  stages {
    stage('build') {
      steps {
        sh './gradlew clean build -x check --parallel'
      }
    }

    stage('test') {
      steps {
        sh './gradlew test'
      }
    }

    stage('upload') {
      steps {
        sh 'aws s3 cp build/libs/delivery.jar s3://jenkins-deploy-myeats/delivery.jar --region ap-northeast-2'
      }
    }

    stage('deploy') {
      steps {
        sh 'aws elasticbeanstalk create-application-version --region ap-northeast-2 --application-name myeats --version-label ${BUILD_TAG} --source-bundle S3Bucket="jenkins-deploy-myeats",S3Key="delivery.jar"'
        sh 'aws elasticbeanstalk update-environment --region ap-northeast-2 --environment-name Myeats-env --version-label ${BUILD_TAG}'
      }
    }

  }
}

```

- 빌드와 테스트 파이프라인을 따로 분리하여 실행되도록 했습니다.
- 빌드 후 생성되는 jar 파일명, 리전, 버킷명, Beanastalk 애플리케이션명, 환경명을 적절하게 수정하여 CLI에서 동작하는지 확인하고 설정합니다.

### 파이프라인 실행을 위해 깃헙 웹훅 설정

- http://Jenkins주소/github-webhook/ 으로 push 이벤트를 발송할 수 있게 웹훅을 설정합니다.

### 참고 자료

- [EB Cli Command, AWS](https://docs.aws.amazon.com/ko_kr/elasticbeanstalk/latest/dg/eb-cli3.html)
- [S3 Cli Command, AWS](https://docs.aws.amazon.com/ko_kr/cli/latest/userguide/cli-services-s3-commands.html)
- [Java Plugin, Gradle](https://docs.gradle.org/current/userguide/java_plugin.html)
- [Elastic Beanstalk와 Jenkins를 이용한 CI/CD 환경 구축, NDS](https://tech.cloud.nongshim.co.kr/2021/11/15/hands-on-elastic-beanstalk%EB%A5%BC-%EC%82%AC%EC%9A%A9%ED%95%9C-%EC%9B%B9-%EC%95%A0%ED%94%8C%EB%A6%AC%EC%BC%80%EC%9D%B4%EC%85%98-%EB%B0%B0%ED%8F%AC-pythondjango-jenkins%EB%A5%BC-%ED%99%9C/)