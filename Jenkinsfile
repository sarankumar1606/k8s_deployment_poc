pipeline {
    agent any
    
    environment {
        DOCKER_IMAGE = 'k8s-helloworld'   // Name of the Docker image
        DOCKER_TAG = 'latest'             // Tag for the image (e.g., 'latest', 'v1.0.0')
        DOCKER_REGISTRY = 'sarannethi' // Optional: If pushing to a private registry
        DOCKERHUB_CREDENTIALS= credentials('dockerhubcredentials')
        KUBECONFIG_CREDENTIAL_ID = 'kubeconfig-prod'
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', credentialsId: '2310c1c0-b41b-41dd-8c28-ab44ddb25822', url: 'https://github.com/sarankumar1606/k8s_deployment_poc.git'
            }
        }
    stage('Build Docker Image') {
            steps {
                script {
                    sh "sudo docker build -t ${DOCKER_IMAGE}:${DOCKER_TAG} ."
                }
            }
        }
        stage('Push Docker Image') {
            steps {
                    script {
                        sh 'echo $DOCKERHUB_CREDENTIALS_PSW | sudo docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
                        sh "sudo docker tag ${DOCKER_IMAGE}:${DOCKER_TAG} ${DOCKER_REGISTRY}/${DOCKER_IMAGE}:${DOCKER_TAG}"
                        sh "sudo docker push ${DOCKER_REGISTRY}/${DOCKER_IMAGE}:${DOCKER_TAG}"
                    }
                }
        }
        stage('Deploy to Kubernetes') {
            steps {
                withKubeConfig([credentialsId: KUBECONFIG_CREDENTIAL_ID]) {
                    sh '''
                    # Use kubectl commands
                    kubectl get nodes
                    kubectl create namespace appdeploy
                    kubectl apply -f k8s-deployment.yaml -n appdeploy
                    
                    '''
                }
            }
        }
        
        }        
    }
