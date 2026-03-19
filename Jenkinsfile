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

        // 🔍 DEBUG STAGE (VERY IMPORTANT)
        stage('Check Files') {
            steps {
                sh '''
                echo "Current Directory:"
                pwd

                echo "Files in Workspace:"
                ls -l

                echo "Full Structure:"
                ls -R
                '''
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
                    docker push $DOCKER_IMAGE:latest
                    '''
                }
            }
        }

        // 🚀 AUTO DETECT DEPLOY
        stage('Deploy to Kubernetes') {
            steps {
                sh '''
                if [ -d "kubernetes" ]; then
                    echo "Using kubernetes folder"
                    kubectl apply -f kubernetes/
                elif [ -d "Kubernetes" ]; then
                    echo "Using Kubernetes folder"
                    kubectl apply -f Kubernetes/
                else
                    echo "Applying YAML files from root"
                    kubectl apply -f deployment.yml
                    kubectl apply -f service.yml
                fi
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
