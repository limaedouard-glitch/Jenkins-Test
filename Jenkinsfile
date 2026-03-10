pipeline {
    agent {
        docker {
            image 'maven:3.9.13-eclipse-temurin-8' // Maven + JDK 8
            args '-v $HOME/.m2:/root/.m2'          // pour cache Maven
        }
    }

    environment {
        DOCKER_HOST = "tcp://192.168.1.181:2375"
    }

    stages {
        stage('Clone Repo') {
            steps {
                git 'https://github.com/limaedouard-glitch/Jenkins-Test.git'
            }
        }

        stage('Build Project') {
            steps {
                // Maven compile + package, tests ignorés
                sh "mvn -B -DskipTests clean package"
            }
        }

        stage('Initialize Docker') {
            steps {
                script {
                    // Si Docker est configuré dans Jenkins comme tool
                    def dockerHome = tool 'MyDocker'
                    env.PATH = "${dockerHome}/bin:${env.PATH}"
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    def dockerImageTag = "devopsexample${env.BUILD_NUMBER}"
                    sh "docker build -t ${dockerImageTag} ."
                }
            }
        }

        stage('Deploy Docker Image') {
            steps {
                script {
                    def dockerImageTag = "devopsexample${env.BUILD_NUMBER}"
                    echo "Docker Image Tag Name: ${dockerImageTag}"
                    sh "docker run --name devopsexample -d -p 2222:2222 ${dockerImageTag}"
                }
            }
        }
    }

    post {
        always {
            echo "Pipeline terminé !"
        }
    }
}
