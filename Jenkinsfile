pipeline {
    agent any

    environment {
        DOCKER_IMAGE_NAME = 'spring-petclinic'
        DOCKERHUB_USERNAME = 'santhoshgullapudi' // Replace with your DockerHub username
        DOCKERHUB_PASSWORD = 'Sanu*2710D' // Jenkins credentials for DockerHub password
        DOCKER_TAG = 'latest' // You can modify this to use dynamic versioning
    }

    stages {
        stage('Checkout') {
            steps {
                // Checkout the Spring PetClinic repository
                git branch: 'main', url: 'https://github.com/santhoshsanu/spc.git'
            }
        }

        stage('Build Application') {
            steps {
                script {
                    // List the directory contents
                    sh './mvnw package'
                }
            }
        }

        // Uncomment and modify below stages as needed

        stage('Build Docker Image') {
            steps {
                script {
                    // Build Docker image
                    sh """
                    whoami
                    docker build -t $DOCKER_IMAGE_NAME:$DOCKER_TAG .
                    """
                }
            }
        }

        stage('Login to DockerHub') {
            steps {
                script {
                    // Log in to DockerHub
                    sh """
                    echo $DOCKERHUB_PASSWORD | docker login -u $DOCKERHUB_USERNAME --password-stdin
                    """
                }
            }
        }

        stage('Push Docker Image to DockerHub') {
            steps {
                script {
                    // Push the Docker image to DockerHub
                    sh """
                    docker push $DOCKER_IMAGE_NAME:$DOCKER_TAG
                    """
                }
            }
        }

        stage('Run Application') {
            steps {
                script {
                    // Run the application using the jar file from target/
                    sh 'docker run -d -p 8090:8080 $DOCKER_IMAGE_NAME:$DOCKER_TAG'
                }
            }
        }
    }  // <-- Closing brace added here to properly close the 'stages' block

    // post {
    //     success {
    //         echo 'Build, Docker image push, and application run completed successfully!'
    //     }
    //     failure {
    //         echo 'Build, Docker image push, or application run failed.'
    //     }
    //     always {
    //         // Clean up Docker images after job
    //         sh 'docker system prune -af'
    //     }
    // }
}
