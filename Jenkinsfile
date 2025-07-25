pipeline {
    agent any 

    tools {
        jdk 'java-11'
        maven 'maven'
    }

    stages {
        stage('Git checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/Hanamanth866/test-1.git'
            }
        }

        stage('Compile') {
            steps {
                sh "mvn compile"
            }
        }

        stage('Build') {
            steps {
                sh "mvn clean install"
            }
        }

        stage('Build Docker Image') {
            steps {
                sh "docker build -t hanamanth866/project:1 ."
            }
        }

        stage('Docker Image Scan') {
            steps {
                sh "trivy image  --skip-files value table -o trivy-image-report.html hanamanth866/project:1"
            }
        }

        stage('Containerization') {
            steps {
                sh '''
                    docker stop c1 || true
                    docker rm c1 || true
                    docker run -it -d --name c1 -p 9002:8080 hanamanth866/project:1
                '''
            }
        }

        stage('Login to Docker Hub') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'docker-hub-credentials', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                        sh "echo $DOCKER_PASSWORD | docker login -u $DOCKER_USERNAME --password-stdin"
                    }
                }
            }
        }

        stage('Push Image to Repository') {
            steps {
                sh 'docker push hanamanth866/project:1'
            }
        }
    }
}
