pipeline {
    agent any

    environment {
        DOCKER_REGISTRY = "aqsaali/helloworld"
        IMAGE_NAME = "aqsaali/helloworld:latest"
        CONTAINER_NAME = "jovial_zhukovsky"
        PREVIOUS_VERSION = sh(script: 'docker ps -q --filter ancestor=${DOCKER_REGISTRY}/${IMAGE_NAME}', returnStatus: true).trim()
    }

    stages {
        stage('Build') {
            steps {
                script {
                    sh 'docker build -t ${DOCKER_REGISTRY}/${IMAGE_NAME}:${BUILD_NUMBER} .'
                }
            }
        }

        stage('Test') {
            steps {
                script {
                    sh 'docker run --rm ${DOCKER_REGISTRY}/${IMAGE_NAME}:${BUILD_NUMBER} your-test-command'
                }
            }
        }

        stage('Deploy') {
            steps {
                script {
                    sh 'docker stop ${CONTAINER_NAME} || true'
                    sh 'docker rm ${CONTAINER_NAME} || true'
                    sh 'docker run -d -p 8080:80 --name ${CONTAINER_NAME} ${DOCKER_REGISTRY}/${IMAGE_NAME}:${BUILD_NUMBER}'
                }
            }
        }
    }

   post {
        failure {
            script {
                // Rollback steps
                if (env.PREVIOUS_VERSION) {
                    echo "Rolling back to the previous version: ${env.PREVIOUS_VERSION}"
                    sh "docker stop ${CONTAINER_NAME} || true"
                    sh "docker rm ${CONTAINER_NAME} || true"
                    sh "docker run -d -p 8080:80 --name ${CONTAINER_NAME} ${DOCKER_REGISTRY}/${IMAGE_NAME}:${env.PREVIOUS_VERSION}"
                } else {
                    echo "No previous version found for rollback."
                }

                // Email notification
                emailext body: 'The deployment failed. Please check the Jenkins build logs.',
                         subject: "Deployment Failure: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                         to: 'fa20-bse-034@cuiatk.edu.pk',
                         mimeType: 'text/html'
            }
        }
    }
}
