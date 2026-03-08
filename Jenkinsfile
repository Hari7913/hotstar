pipeline {
    agent any

    environment {
        IMAGE_NAME = "lucky7913/hotstar-app"
        TAG = "latest"
        DOCKER_CREDS = "dockerhub-cred"
    }

    stages {

        stage('Git Checkout') {
            steps {
                git branch: 'main',
                url: 'https://github.com/Hari7913/hotstar.git'
            }
        }

        stage('Build Maven') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $IMAGE_NAME:$TAG .'
            }
        }

        stage('Docker Login') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-cred',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {

                    sh '''
                    echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
                    '''
                }
            }
        }

        stage('Push Image to DockerHub') {
            steps {
                sh 'docker push $IMAGE_NAME:$TAG'
            }
        }

        stage('Deploy Container') {
            steps {
                sh '''
                docker stop hotstar-container || true
                docker rm hotstar-container || true

                docker run -d \
                -p 8089:8080 \
                --name hotstar-container \
                $IMAGE_NAME:$TAG
                '''
            }
        }
    }

}
