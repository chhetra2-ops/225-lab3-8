pipeline {
    agent any

    environment {
        DOCKER_CREDENTIALS_ID = 'roseaw-dockerhub'
        GITHUB_URL = 'https://github.com/chhetra2-ops/225-lab3-8.git'
        KUBECONFIG = credentials('roseaw-225')
    }

    stages {
        stage('Checkout') {
            steps {
                checkout([
                    $class: 'GitSCM',
                    branches: [[name: '*/main']],
                    userRemoteConfigs: [[
                        url: "${GITHUB_URL}",
                        credentialsId: 'roseaw-dockerhub'
                    ]]
                ])
            }
        }

        stage('Build Mongo Stateful Set') {
            steps {
                sh 'kubectl apply -f mongo-secret.yaml'
                sh 'kubectl apply -f mongo.yaml'
                sh 'kubectl apply -f mongo-configmap.yaml'
                sh 'kubectl apply -f mongo-express.yaml'
            }
        }

        stage('Check Kubernetes Cluster') {
            steps {
                sh 'kubectl get all'
            }
        }
    }

    post {
        success {
            slackSend(
                color: "good",
                message: "Build Completed Successfully: ${env.JOB_NAME} #${env.BUILD_NUMBER}"
            )
        }
        unstable {
            slackSend(
                color: "warning",
                message: "Build Unstable: ${env.JOB_NAME} #${env.BUILD_NUMBER}"
            )
        }
        failure {
            slackSend(
                color: "danger",
                message: "Build Failed: ${env.JOB_NAME} #${env.BUILD_NUMBER}"
            )
        }
    }
}
