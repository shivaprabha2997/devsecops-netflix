pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "veeradockerhub/netflix-clone"
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

        stage('Docker Login & Push (Fully Automated)') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker-creds', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
                    sh '''
                    rm -rf ~/.docker

                    echo "$PASS" | docker login -u "$USER" --password-stdin

                    docker push $DOCKER_IMAGE:latest
                    '''
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh '''
                kubectl apply -f Kubernetes/deployment.yaml
                kubectl apply -f Kubernetes/service.yaml
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
            echo "✅ Full CI/CD Pipeline Success!"
        }
        failure {
            echo "❌ Pipeline Failed!"
        }
    }
}
