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
    image: alpine
    command: ['sh', '-c', 'cat']
    tty: true

  - name: kaniko
    image: gcr.io/kaniko-project/executor:latest
    args:
      - "--dockerfile=/workspace/Dockerfile"
      - "--context=dir:///workspace"
      - "--destination=docker.io/megs17/myapp:latest"
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
        container('alpine') {
          checkout scm
        }
      }
    }

    stage('Build & Push') {
      steps {
        container('alpine') {
          sh '''
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

  environment {
    GIT_COMMIT_SHORT = sh(script: "git rev-parse --short HEAD", returnStdout: true).trim()
    DOCKER_IMAGE = "megs17/myapp:${GIT_COMMIT_SHORT}"
  }
}
