pipeline {
  agent {
    kubernetes {
      yaml """
apiVersion: v1
kind: Pod
spec:
  serviceAccountName: ecr-access-sa 
  containers:
  - name: kaniko
    image: gcr.io/kaniko-project/executor:debug
    imagePullPolicy: Always
    command:
    - sleep
    args:
    - "9999999"
"""
    }
  }

  environment {
    GIT_COMMIT_SHORT = sh(script: "git rev-parse --short HEAD", returnStdout: true).trim()
    DOCKER_IMAGE = "891076991696.dkr.ecr.us-east-1.amazonaws.com/app:0.1.${env.BUILD_NUMBER}"
  }

  stages {
    stage('Build & Push with Kaniko') {
      steps {
        container(name: 'kaniko', shell: '/busybox/sh') {
          checkout scm   // lets Jenkins handle the checkout and enter the workspace
          sh '''
         /kaniko/executor \
            --context `pwd` \
            --dockerfile Dockerfile \
            --destination=$DOCKER_IMAGE \
            --cleanup \
          '''
        }
      }
    }
  }
}
// kubectl create secret docker-registry docker-credentials --docker-username=[userid] --docker-password=[Docker Hub access token] --docker-email=[user email address]  
// Uses /busybox/sh as the shell, because the base image doesn't include bash or sh by default

