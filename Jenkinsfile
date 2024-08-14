pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'akshaykomath/node-webapp:latest'
        DOCKER_CREDENTIALS_ID = 'dockerhub-credentials-id'
        KUBECONFIG = '/var/lib/jenkins/.kube/config'  // Path to the kubeconfig file
        GIT_CREDENTIALS_ID = 'git-credentials-id'
        GIT_REPO = 'https://github.com/akshaykomath15/nodejs-webapp.git'
    }

    stages {
        stage('Checkout') {
            steps {
                git credentialsId: "${GIT_CREDENTIALS_ID}", url: "${GIT_REPO}", branch: 'master'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    docker.build("${DOCKER_IMAGE}")
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', "${DOCKER_CREDENTIALS_ID}") {
                        docker.image("${DOCKER_IMAGE}").push('latest')
                    }
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                script {
                    sh 'kubectl apply -f deployment.yaml'
                    sh 'kubectl apply -f service.yaml'
                }
            }
        }

        stage('Verify Kubernetes Access') {
            steps {
                script {
                    sh 'kubectl get nodes'
                }
            }
        }
    }

    post {
        always {
            cleanWs()
        }
    }
}
