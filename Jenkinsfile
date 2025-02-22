pipeline {
    agent any

    environment {
        DOCKER_IMAGE_NAME = 'spring-petclinic'
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
                    // List the directory contents and build the application
                    sh './mvnw package'
                }
            }
        }

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


        stage('Login to Docker') {
            steps {
                script {
                    // Login to DockerHub using credentials stored in Jenkins
                    withCredentials([usernamePassword(credentialsId: 'Docker-credentials', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                        sh """
                        echo $DOCKER_PASSWORD | docker login -u $DOCKER_USERNAME --password-stdin
                        """
                    }
                }
            }
        }

        stage('Push Docker Image to DockerHub') {
            steps {
                script {
                    // Tag and push the Docker image to DockerHub
                    sh """
                    docker tag $DOCKER_IMAGE_NAME:$DOCKER_TAG $DOCKER_USERNAME/$DOCKER_IMAGE_NAME:$DOCKER_TAG
                    docker push $DOCKER_USERNAME/$DOCKER_IMAGE_NAME:$DOCKER_TAG
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
    }

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
