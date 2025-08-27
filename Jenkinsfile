pipeline {
  agent {
    docker {
      image 'docker:latest'
      args '-v /var/run/docker.sock:/var/run/docker.sock'
    }
}

  environment {
    IMAGE_NAME = "myapp"
    DOCKER_REGISTRY = "docker.io/tomlinmathew"
  }

  stages {
    stage('Clone Repo') {
      steps {
        git branch: 'main', credentialsId: 'github-creds', url: 'https://github.com/tomlinmathew/DockerProject.git'
      }
    }

    stage('Build Docker Image') {
      steps {
        sh 'docker build -t $IMAGE_NAME .'
      }
    }

    stage('Push to DockerHub') {
      steps {
        withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
          sh 'echo $PASS | docker login -u $USER --password-stdin'
          sh 'docker tag $IMAGE_NAME $DOCKER_REGISTRY/$IMAGE_NAME:latest'
          sh 'docker push $DOCKER_REGISTRY/$IMAGE_NAME:latest'
        }
      }
    }

    stage('Deploy') {
      steps {
        sh 'docker run -d -p 5000:5000 $DOCKER_REGISTRY/$IMAGE_NAME:latest'
      }
    }
  }
}
