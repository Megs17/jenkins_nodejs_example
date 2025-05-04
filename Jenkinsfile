pipeline {
  agent {
    kubernetes {
      label 'kaniko-agent'
      defaultContainer 'kaniko'
      yaml """
apiVersion: v1
kind: Pod
spec:
  containers:
  - name: kaniko
    image: gcr.io/kaniko-project/executor:latest
    command:
    - echo
    tty: true
    volumeMounts:
    - name: kaniko-secret
      mountPath: /kaniko/.docker
  volumes:
  - name: kaniko-secret
    projected:
      sources:
      - secret:
          name: docker-config
"""
    }
  }

  environment {
    GIT_COMMIT_SHORT = sh(script: "git rev-parse --short HEAD", returnStdout: true).trim()
    DOCKER_IMAGE = "megs17/myapp:${GIT_COMMIT_SHORT}"
  }
  


  stages {
    stage('Checkout') {
      steps {
        container('kaniko') {
          checkout scm
        }
      }
    }

    stage('Build & Push with Kaniko') {
      steps {
        container('kaniko') {
          sh '''
            /kaniko/executor \
              --context `pwd` \
              --dockerfile `pwd`/Dockerfile \
              --destination=docker.io/$DOCKER_IMAGE \
              --cleanup
          '''
        }
      }
    }
  }
}
