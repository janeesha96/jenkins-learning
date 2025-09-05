pipeline {
    agent {
        node {
            // ⚠️ Replace 'built-in' with the actual label from your Jenkins node config
            label 'built-in'  
            customWorkspace "/Users/janeeshawishmika/.jenkins/workspace/${JOB_NAME}"
        }
    }

    options {
        disableConcurrentBuilds()
        durabilityHint 'PERFORMANCE_OPTIMIZED'
    }

    environment {
        DOCKER_BFLASK_IMAGE = 'janeesha/my-flask-app:latest'
        DOCKER_REGISTRY_CREDS = 'docker-jenkins-token-1'
    }

    stages {
        stage('Debug') {
            steps {
                sh 'pwd'
                sh 'ls -la'
            }
        }

        stage('Clean') {
            steps {
                deleteDir()
            }
        }

        stage('Build') {
            steps {
                script {
                    sh '''#!/bin/bash
                    docker build -t $DOCKER_BFLASK_IMAGE .
                    '''
                }
            }
        }

        stage('Test') {
            steps {
                script {
                    sh '''#!/bin/bash
                    docker run --rm $DOCKER_BFLASK_IMAGE python -m pytest app/tests/
                    '''
                }
            }
        }

        stage('Deploy') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: "${DOCKER_REGISTRY_CREDS}",
                    passwordVariable: 'DOCKER_PASSWORD',
                    usernameVariable: 'DOCKER_USERNAME'
                )]) {
                    script {
                        sh '''#!/bin/bash
                        echo $DOCKER_PASSWORD | docker login -u $DOCKER_USERNAME --password-stdin
                        docker push $DOCKER_BFLASK_IMAGE
                        '''
                    }
                }
            }
        }
    }

    post {
        always {
            sh 'docker logout || true'
            sh 'docker system prune -f || true'
        }
    }
}
