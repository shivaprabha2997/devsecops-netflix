pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "veeradockerhub/netflix-clone"
        KUBE_CONFIG = "/root/.kube/config" // path to your kubeconfig
    }

    stages {

        stage('Clean Workspace') {
            steps {
                cleanWs()
            }
        }

        stage('Clone Code') {
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
                sh 'docker build -t $DOCKER_IMAGE:latest .'
            }
        }

        stage('Docker Login & Push') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker-creds', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
                    sh '''
                    rm -rf ~/.docker
                    echo "$PASS" | docker login -u "$USER" --password-stdin
                    docker image prune -af   # delete old images
                    docker push $DOCKER_IMAGE:latest
                    '''
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                // Ensure kubeconfig is available
                sh 'export KUBECONFIG=$KUBE_CONFIG'

                // Apply deployment and service
                sh '''
                kubectl apply -f Kubernetes/deployment.yml
                kubectl apply -f Kubernetes/service.yml
                kubectl rollout status deployment/netflix-app
                '''
            }
        }

        stage('Verify Deployment') {
            steps {
                sh '''
                kubectl get pods
                kubectl get svc
                '''
            }
        }
    }

    post {
        success {
            echo "✅ Full CI/CD Pipeline Success! Your app should be live with LoadBalancer."
        }
        failure {
            echo "❌ Pipeline Failed!"
        }
    }
}
