pipeline {
    agent any

    environment {
        IMAGE_NAME = "devops-app"
        APP_PATH = "/opt/devops-project/app"
    }

    stages {

        stage('Verify Path') {
            steps {
                sh 'ls -la /opt/devops-project'
                sh 'ls -la ${APP_PATH}'
            }
        }

        stage('Build Image in Minikube') {
            steps {
                sh '''
                eval $(minikube docker-env)
                docker build -t devops-app ${APP_PATH}
                '''
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh '''
                kubectl apply -f /opt/devops-project/k8s/
                '''
            }
        }

        stage('Verify Deployment') {
            steps {
                sh 'kubectl get pods'
                sh 'kubectl get svc'
            }
        }
    }
}
