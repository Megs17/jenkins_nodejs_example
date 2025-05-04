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
    args:
    - "--dockerfile=Dockerfile"
    - "--context=dir://workspace"
    - "--destination=docker.io/megs17/myapp:latest"
    - "--cleanup"
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

  stages {
    stage('Checkout') {
      steps {
        container('kaniko') {
          checkout scm
        }
      }
    }
  }
}
