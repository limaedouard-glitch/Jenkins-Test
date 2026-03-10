pipeline {
    agent any

    environment {
        // On suppose que Maven et Docker sont configurés dans Jenkins sous ces noms
        MVN_HOME = tool name: 'maven-3.9.13', type: 'maven'
        DOCKER_HOME = tool name: 'MyDocker', type: 'docker'
        PATH = "${env.MVN_HOME}/bin:${env.DOCKER_HOME}/bin:${env.PATH}"
    }

    stages {
        stage('Clone Repo') {
            steps {
                git url: 'https://github.com/limaedouard-glitch/Jenkins-Test.git'
            }
        }

        stage('Build Project') {
            steps {
                sh "${env.MVN_HOME}/bin/mvn -B -DskipTests clean package"
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    def dockerImageTag = "devopsexample:${env.BUILD_NUMBER}"
                    sh "docker build -t ${dockerImageTag} ."
                }
            }
        }

        stage('Run Docker Container') {
            steps {
                script {
                    def dockerImageTag = "devopsexample:${env.BUILD_NUMBER}"
                    sh "docker run --name devopsexample -d -p 2222:2222 ${dockerImageTag}"
                }
            }
        }
    }

    post {
        always {
            echo "Pipeline terminé !"
        }
        failure {
            echo "Le build a échoué !"
        }
    }
}
