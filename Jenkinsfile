pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'your-dockerhub-username/hello-world'
        DOCKER_CREDENTIALS = 'docker-hub-credentials-id' // Add credentials in Jenkins
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    docker.build(DOCKER_IMAGE)
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                withDockerRegistry([credentialsId: DOCKER_CREDENTIALS, url: 'https://index.docker.io/v1/']) {
                    script {
                        docker.image(DOCKER_IMAGE).push('latest')
                    }
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                withKubeConfig([credentialsId: 'kubeconfig-credentials-id']) {
                    sh '''
                    kubectl apply -f k8s-deployment.yaml
                    '''
                }
            }
        }
    }

    post {
        success {
            echo "Pipeline completed successfully!"
        }
        failure {
            echo "Pipeline failed!"
        }
    }
}
