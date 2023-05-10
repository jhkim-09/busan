pipeline {
  agent none

  environment {
    registry = "https://index.docker.io/v1/"
    imageName = "kimjuhyo/test-cicd"
  }

  stages {
    stage('Checkout') {
      agent any
      steps {
        git branch: 'main', url: 'https://github.com/jhkim-09/busan.git'
      }
    }
    stage('Build') {
      agent {
        docker { image 'maven:3-openjdk-8' }
      }
      steps {
        sh 'mvn -DskipTests=true clean package'
      }
    }
    stage('Test') {
      agent {
        docker { image 'maven:3-openjdk-8' }
      }
      steps {
        sh 'mvn test'
      }
    }
    stage('Build Docker Image') {
        agent any
        steps {
            sh 'docker image build -t $imageName:$BUILD_NUMBER .'
        }
    }
    stage('Tag Docker Image') {
        agent any
        steps {
            sh 'docker image tag $imageName:$BUILD_NUMBER $imageName:latest'
        }
    }
    stage('Publish Docker Image') {
        agent any
        steps {
            withDockerRegistry(credentialsId: 'docker-hub-token', url: "$registry") {
              sh 'docker image push $imageName:$BUILD_NUMBER'
              sh 'docker image push $imageName:latest'
            }
        }
    }
    stage('Run Docker Container') {
        agent any
        steps {
            sh "docker -H tcp://192.168.56.10:2375 container run -d --name myweb -p 80:8080 $imageName:latest"
        }
    }
  }
}
