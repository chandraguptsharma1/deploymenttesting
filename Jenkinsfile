pipeline {
  agent any

  environment {
    DOCKER_HUB_USER = 'samchandra1100'
    IMAGE_NAME = 'deploymenttesting'
    IMAGE_TAG = 'latest'
    DOCKER_IMAGE = "${DOCKER_HUB_USER}/${IMAGE_NAME}:${IMAGE_TAG}" ///
  }

  triggers {
    githubPush()
  }

  stages {
    stage('Checkout Code') {
      steps {
        git 'https://github.com/chandraguptsharma1/deploymenttesting.git'
      }
    }

    stage('Install Dependencies') {
      steps {
        sh 'npm install'
      }
    }

    stage('Build Angular App') {
      steps {
        sh 'npm run build --prod'
      }
    }

    stage('Build Docker Image') {
      steps {
        sh 'docker build -t $DOCKER_IMAGE .'
      }
    }

    stage('Push Docker Image') {
      steps {
       withCredentials([string(credentialsId: 'dockerhub-token', variable: 'DOCKER_TOKEN')]) {
         sh '''
         echo "$DOCKER_TOKEN" | docker login -u samchandra1100 --password-stdin
         docker push samchandra1100/deploymenttesting:latest
         '''
        }

      }
    }

    stage('Deploy Locally via Docker') {
      steps {
        sh '''
          docker stop deploymenttesting || true
          docker rm deploymenttesting || true
          docker run -d -p 4200:80 --name deploymenttesting $DOCKER_IMAGE
        '''
      }
    }
  }
}
