pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "farahkanoun/monapp:${BUILD_NUMBER}"
    }

    stages {

        stage('Checkout from GitHub') {
            steps {
                git 'https://github.com/Farah1805/projet-cicd'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    dockerImage = docker.build("${DOCKER_IMAGE}")
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    docker.withRegistry('', 'dockerhub-creds') {
                        dockerImage.push("${BUILD_NUMBER}")
                        dockerImage.push("latest")
                    }
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                withCredentials([file(credentialsId: 'kubeconfig-fixed', variable: 'KUBECONFIG')]) {
                    sh 'kubectl version --client'
                    sh "kubectl set image deployment/mon-appli monconteneur=${DOCKER_IMAGE}"
                    sh 'kubectl config view'
                    sh 'kubectl get nodes'
                }
            }
        }
    }

    post {
        success {
            echo 'Pipeline réussi !'
        }
        failure {
            echo 'Le pipeline a echoue.'
        }
    }
}
