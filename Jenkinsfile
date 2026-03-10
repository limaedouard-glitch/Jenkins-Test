pipeline {
    agent {
        docker {
            image 'maven:3.9.9-eclipse-temurin-17'
            args '--privileged -v /var/run/docker.sock:/var/run/docker.sock'
        }
    }

    environment {
        dockerImageTag = "devopsexample${env.BUILD_NUMBER}"
    }

    stages {
        stage('Clone Repo') {
            steps {
                git 'https://github.com/limaedouard-glitch/Jenkins-Test.git'
            }
        }

        stage('Build Project') {
            steps {
                sh 'mvn -B -DskipTests clean package'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh "docker build -t ${dockerImageTag} ."
            }
        }

        stage('Deploy Docker Image') {
            steps {
                echo "Docker Image Tag: ${dockerImageTag}"
                // Si un container du même nom existe déjà, on le supprime
                sh "docker rm -f devopsexample || true"
                sh "docker run --name devopsexample -d -p 2222:2222 ${dockerImageTag}"
            }
        }
    }

    post {
        always {
            echo 'Pipeline terminé !'
        }
    }
}
