pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'aqsaali/dockerterminal1:latest'
        PREVIOUS_DOCKER_IMAGE = 'aqsaali/dockerterminal1:previous'
    }

    stages {
        stage('Checkout') {
            steps {
                // Check out the source code from your version control system
                checkout scm
            }
        }

        stage('Build') {
            steps {
                // Build the Docker image
                script {
                    docker.build(DOCKER_IMAGE)
                }
            }
        }

        stage('Test') {
            steps {
                // Add test steps here
            }
        }

        stage('Deploy') {
            steps {
                // Log in to Docker Hub and push the Docker image
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'docker-hub-credentials') {
                        docker.image(DOCKER_IMAGE).push()
                    }
                }
            }
        }
    }

     post {
        failure {
            // Rollback to the previous version on failure
            echo 'Deployment failed. Rolling back to the previous version.'
            // Implement rollback logic here
            script {
                // Pull the previous version from Docker Hub and run it
                docker.withRegistry('https://registry.hub.docker.com', 'docker-hub-credentials') {
                    docker.image(PREVIOUS_DOCKER_IMAGE).pull()
                    docker.image(PREVIOUS_DOCKER_IMAGE).run()
                }
            }
        }


        success {
            // Notify the team or perform other actions on success
            echo 'Deployment successful. Notify the team.'
        }
    }
}
