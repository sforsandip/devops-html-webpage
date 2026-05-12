pipeline {
    agent any

    environment {
        IMAGE_NAME = "devops-html-app"
        IMAGE_TAG = "1.0"
        NEXUS_URL = "172.31.21.36:8082"
        NEXUS_REPO = "devops-html-app"
    }

    stages {

        stage('Checkout Code') {
            steps {
                 git branch: 'main',
               url: 'git@github.com:sforsandip/devops-html-webpage.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                dir('app') {
            sh 'docker build -t devops-html-app:1.0 .'
        }
    }
}

        stage('Login to Nexus') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'nexus-creds', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
                    sh """
                        echo $PASS | docker login ${NEXUS_URL} -u $USER --password-stdin
                    """
                }
            }
        }

        stage('Tag Image') {
            steps {
                sh """
                    docker tag ${IMAGE_NAME}:${IMAGE_TAG} ${NEXUS_URL}/${NEXUS_REPO}:${IMAGE_TAG}
                """
            }
        }

        stage('Push Image to Nexus') {
            steps {
                sh """
                    docker push ${NEXUS_URL}/${NEXUS_REPO}:${IMAGE_TAG}
                """
            }
        }

        stage('Run Container (Local Test)') {
            steps {
                sh """
                    docker run -d -p 8080:80 --name html-app ${IMAGE_NAME}:${IMAGE_TAG} || true
                """
            }
        }
    }

    post {
        success {
            echo "✅ Pipeline executed successfully!"
        }
        failure {
            echo "❌ Pipeline failed. Check logs."
        }
    }
}
