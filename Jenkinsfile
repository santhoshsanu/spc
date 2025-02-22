pipeline {
    agent any

    environment {
        DOCKER_IMAGE_NAME = 'spring-petclinic'
        DOCKER_TAG = 'latest' // Modify this for dynamic versioning if needed
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/santhoshsanu/spc.git'
            }
        }

        stage('Build Application') {
            steps {
                script {
                    sh './mvnw package'
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
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
            withCredentials([usernamePassword(credentialsId: 'Docker-credentials', 
                                             usernameVariable: 'DOCKERHUB_USERNAME', 
                                             passwordVariable: 'DOCKERHUB_PASSWORD')]) {
                sh '''
                echo "$DOCKERHUB_PASSWORD" | docker login -u "$DOCKERHUB_USERNAME" --password-stdin
                '''
            }
        }
    }
}


        stage('Push Docker Image to DockerHub') {
            steps {
                script {
                    sh """
                    docker tag $DOCKER_IMAGE_NAME:$DOCKER_TAG $DOCKERHUB_USERNAME/$DOCKER_IMAGE_NAME:$DOCKER_TAG
                    docker push $DOCKERHUB_USERNAME/$DOCKER_IMAGE_NAME:$DOCKER_TAG
                    """
                }
            }
        }

        stage('Run Application') {
            steps {
                script {
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
    //         sh 'docker system prune -af'
    //     }
    // }
}
