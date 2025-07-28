pipeline {
    agent any

    environment {
        IMAGE_NAME = 'ganeshpatil2123/hello-website'
        TAG = "${BUILD_NUMBER}"
        DOCKERHUB_CREDENTIALS = 'dockerhub-credentials' // ID of DockerHub credentials in Jenkins
    }

    stages {
        stage('Clone Repository') {
            steps {
                git 'https://github.com/ganesh2123/Myintro.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh "docker build -t $IMAGE_NAME:$TAG ."
                }
            }
        }

        stage('Push to DockerHub') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: DOCKERHUB_CREDENTIALS, usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                        sh "echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin"
                        sh "docker push $IMAGE_NAME:$TAG"
                    }
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh 'kubectl apply -f deployment.yaml'
                sh 'kubectl apply -f service.yaml'
            }
        }

        stage('Notify by Email') {
            steps {
                emailext (
                    subject: "Deployed Successfully: Build #${BUILD_NUMBER}",
                    body: "Project has been deployed successfully.",
                    recipientProviders: [developers(), requestor()]
                )
            }
        }
    }
}
