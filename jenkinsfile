pipeline {
    agent any

    environment {
        IMAGE_NAME = "devops-app"
        CONTAINER_NAME = "devops-container"
        PORT = "8085"
        APP_PATH = "/opt/devops-project/app"
    }

    stages {

        stage('Verify Path') {
            steps {
                sh 'ls -la /opt/devops-project'
                sh 'ls -la ${APP_PATH}'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t ${IMAGE_NAME} ${APP_PATH}'
            }
        }

        stage('Clean Old Container') {
    steps {
        sh '''
        docker rm -f ${CONTAINER_NAME} || true
        '''
    }
}

        stage('Run Container') {
            steps {
                sh '''
                docker run -d -p ${PORT}:80 --name ${CONTAINER_NAME} ${IMAGE_NAME}
                '''
            }
        }
    }
}
