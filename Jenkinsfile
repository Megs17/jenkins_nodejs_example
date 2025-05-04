pipeline {
  agent {
    kubernetes {
      yaml """
apiVersion: v1
kind: Pod
spec:
  containers:
  - name: kaniko
    image: gcr.io/kaniko-project/executor:debug
    imagePullPolicy: Always
    command:
    - sleep
    args:
    - "9999999"
    volumeMounts:
    - name: jenkins-docker-cfg
      mountPath: /kaniko/.docker
  volumes:
  - name: jenkins-docker-cfg
    projected:
      sources:
      - secret:
          name: docker-credentials
          items:
          - key: .dockerconfigjson
            path: config.json
"""
    }
  }

  environment {
    GIT_COMMIT_SHORT = sh(script: "git rev-parse --short HEAD", returnStdout: true).trim()
    DOCKER_IMAGE = "megs17/myapp:${GIT_COMMIT_SHORT}"
  }

  stages {
    stage('Build & Push with Kaniko') {
      steps {
        container(name: 'kaniko', shell: '/busybox/sh') {
          checkout scm
          sh '''
         /kaniko/executor \
            --context `pwd` \
            --dockerfile dockerfile \
            --destination=docker.io/$DOCKER_IMAGE \
            --cleanup \
          '''
        }
      }
    }
  }
}
