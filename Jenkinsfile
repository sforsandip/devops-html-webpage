pipeline {
    agent any

    environment {
        APP_PATH = "/opt/devops-project/app"
    }

stage('Setup Server with Ansible') {
    steps {
        sh '''
        cd /home/ubuntu/ansible-project
        ansible-playbook -i inventory.ini setup-devops.yml
        '''
    }
}

    stages {

        stage('Build Image in Minikube') {
            steps {
                sh '''
                eval $(minikube -p minikube docker-env)
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

        stage('Verify') {
            steps {
                sh 'kubectl get pods'
            }
        }
    }
}
