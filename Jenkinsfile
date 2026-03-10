pipeline {
    agent {
        kubernetes {
            yaml """
apiVersion: v1
kind: Pod
spec:
  containers:
  - name: maven
    image: maven:3.9.9-eclipse-temurin-17
    command:
    - cat
    tty: true
  - name: kaniko
    image: gcr.io/kaniko-project/executor:latest
    command:
    - cat
    tty: true
"""
        }
    }

    environment {
        dockerImageTag = "devopsexample${env.BUILD_NUMBER}"
        DOCKER_REGISTRY = "localhost:5000" // ou ton registre Minikube local
    }

    stages {
        stage('Clone Repo') {
            steps {
                container('maven') {
                    git 'https://github.com/limaedouard-glitch/Jenkins-Test.git'
                }
            }
        }

        stage('Build Project') {
            steps {
                container('maven') {
                    sh 'mvn -B -DskipTests clean package'
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                container('kaniko') {
                    sh """
                    /kaniko/executor \\
                        --dockerfile=Dockerfile \\
                        --context=${WORKSPACE} \\
                        --destination=${DOCKER_REGISTRY}/${dockerImageTag} \\
                        --insecure
                    """
                }
            }
        }

        stage('Deploy Docker Image') {
            steps {
                // Optionnel : utiliser kubectl pour créer un pod/deployment avec cette image
                sh """
                kubectl run devopsexample-${BUILD_NUMBER} \\
                    --image=${DOCKER_REGISTRY}/${dockerImageTag} \\
                    --port=2222
                """
            }
        }
    }

    post {
        always {
            echo "Pipeline terminé ! Image: ${dockerImageTag}"
        }
    }
}
