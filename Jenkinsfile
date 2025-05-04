pipeline {
  agent {
    kubernetes {
      label 'kaniko-agent'
      defaultContainer 'alpine'
      yaml """
apiVersion: v1
kind: Pod
spec:
  containers:
  - name: alpine
    image: alpine:3.18
    command: ['sh', '-c', 'cat']
    tty: true
    volumeMounts:
    - name: kaniko-volume
      mountPath: /kaniko
    - name: kaniko-secret
      mountPath: /kaniko/.docker

  initContainers:
  - name: kaniko-init
    image: gcr.io/kaniko-project/executor:latest
    command: ['cp', '/kaniko/executor', '/kaniko/executor']
    volumeMounts:
    - name: kaniko-volume
      mountPath: /kaniko

  volumes:
  - name: kaniko-volume
    emptyDir: {}
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
    DOCKER_IMAGE = "megs17/myapp:\${GIT_COMMIT_SHORT}"
  }

  stages {
    stage('Checkout') {
      steps {
        container('alpine') {
          checkout scm
        }
      }
    }

    stage('Build & Push with Kaniko') {
      steps {
        container('alpine') {
          sh '''
            chmod +x /kaniko/executor
            /kaniko/executor \
              --dockerfile=Dockerfile \
              --context=`pwd` \
              --destination=docker.io/$DOCKER_IMAGE \
              --cleanup
          '''
        }
      }
    }
  }
}
