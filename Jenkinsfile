pipeline {
    agent any

    environment {
        // Chemin vers Maven installé dans Jenkins (nom configuré dans Global Tool Configuration)
        MVN_HOME = tool 'maven-3.9.13'
        // Docker va utiliser le socket de l'hôte
        DOCKER_HOST = 'unix:///var/run/docker.sock'
    }

    stages {
        stage('Clone Repo') {
            steps {
                git 'https://github.com/limaedouard-glitch/Jenkins-Test.git'
            }
        }

        stage('Build with Maven') {
            steps {
                sh "'${MVN_HOME}/bin/mvn' -B -DskipTests clean package"
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    def dockerImageTag = "devopsexample:${env.BUILD_NUMBER}"
                    sh "docker build -t ${dockerImageTag} ."
                    env.DOCKER_IMAGE_TAG = dockerImageTag
                }
            }
        }

        stage('Run Docker Container') {
            steps {
                sh "docker run --rm --name devopsexample -d -p 2222:2222 ${env.DOCKER_IMAGE_TAG}"
            }
        }
    }

    post {
        always {
            echo "Pipeline terminé !"
        }
    }
}
