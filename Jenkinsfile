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
    - cat
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
    DOCKER_IMAGE = 'yourdockerhubusername/myapp:latest'
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
