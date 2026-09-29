pipeline {
    agent any

    environment {
        IMAGE_NAME = "vrak45/python-cicd-app"
    }

    stages {

        stage('Checkout') {
            steps {
                echo 'Checking out source code from GitHub'
                checkout scm
            }
        }

        stage('Build') {
            steps {
                echo 'Checking Python application'
                sh 'python3 -m py_compile app.py'
            }
        }

        stage('Test') {
            steps {
                echo 'Running Python tests'

                sh '''
                    python3 -m venv venv
                    ./venv/bin/pip install --upgrade pip
                    ./venv/bin/pip install -r requirements.txt
                    ./venv/bin/pytest
                '''
            }
        }

        stage('Docker Build') {
            steps {
                echo 'Building Docker image'

                sh '''
                    docker build -t $IMAGE_NAME:$BUILD_NUMBER .
                    docker tag $IMAGE_NAME:$BUILD_NUMBER $IMAGE_NAME:latest
                '''
            }
        }

        stage('Docker Push') {
            steps {
                echo 'Pushing Docker image to Docker Hub'

                withCredentials([
                    usernamePassword(
                        credentialsId: 'dockerhub-credentials',
                        usernameVariable: 'DOCKER_USERNAME',
                        passwordVariable: 'DOCKER_PASSWORD'
                    )
                ]) {

                    sh '''
                        echo "$DOCKER_PASSWORD" | docker login \
                        -u "$DOCKER_USERNAME" \
                        --password-stdin

                        docker push $IMAGE_NAME:$BUILD_NUMBER
                        docker push $IMAGE_NAME:latest
                    '''
                }
            }
        }

    }
}
