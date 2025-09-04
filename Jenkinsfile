pipeline {
    agent any

    environment {
        DOCKER_BFLASK_IMAGE = 'janeesha/my-flask-app:latest'
        DOCKER_REGISTRY_CREDS = 'docker-jenkins-token-1'
    }

    stages {
        stage('Build') {
            steps {
                sh 'docker build -t my-flask-app .'
                sh 'docker tag my-flask-app $DOCKER_BFLASK_IMAGE'
            }
        }

        stage('Test') {
            steps {
                sh 'docker run my-flask-app python -m pytest app/tests/'
            }
        }

        stage('Deploy') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: "${DOCKER_REGISTRY_CREDS}",
                    passwordVariable: 'DOCKER_PASSWORD',
                    usernameVariable: 'DOCKER_USERNAME'
                )]) {
                    sh """
                        echo \$DOCKER_PASSWORD | docker login -u \$DOCKER_USERNAME --password-stdin docker.io
                        docker push $DOCKER_BFLASK_IMAGE
                    """
                }
            }
        }
    }

    post {
        always {
            sh 'docker logout'
        }
    }
}

