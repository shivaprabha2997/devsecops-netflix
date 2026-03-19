pipeline {
    agent any

    environment {
        DOCKER_HUB_USER = 'veeradockerhub'
        DOCKER_HUB_PASS = credentials('docker-hub-pass') // Replace with your Jenkins secret ID
        IMAGE_NAME = 'netflix-clone'
        K8S_DEPLOYMENT = 'k8s/deployment.yaml'
        K8S_SERVICE = 'k8s/service.yaml'
    }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/veeraprasadkoduri-cmd/devsecops-netflix.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh """
                   docker build -t $DOCKER_HUB_USER/$IMAGE_NAME:latest .
                """
            }
        }

        stage('Docker Login & Push') {
            steps {
                withCredentials([string(credentialsId: 'docker-hub-pass', variable: 'PASS')]) {
                    sh """
                        echo $PASS | docker login -u $DOCKER_HUB_USER --password-stdin
                        docker push $DOCKER_HUB_USER/$IMAGE_NAME:latest
                    """
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh """
                   kubectl apply -f $K8S_DEPLOYMENT
                   kubectl apply -f $K8S_SERVICE
                """
            }
        }

        stage('Verify Deployment') {
            steps {
                sh """
                   kubectl get pods
                   kubectl get svc
                """
            }
        }
    }

    post {
        success {
            echo "✅ Pipeline completed successfully!"
        }
        failure {
            echo "❌ Pipeline failed!"
        }
    }
}
