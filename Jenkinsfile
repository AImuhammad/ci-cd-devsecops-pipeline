 pipeline {
    agent any

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main',
                    credentialsId: 'github-token',
                    url: 'https://github.com/AImuhammad/ci-cd-devsecops-pipeline.git'
            }
        }

        stage('Test') {
            steps {
                sh '''
                        rm -rf venv
                        python3 -m venv venv
                        venv/bin/python -m pip install --upgrade pip
                        venv/bin/python -m pip install -r app/requirements.txt
                        venv/bin/python -m pytest
                '''
            }
        }

        stage('Docker Build') {
            steps {
                sh '''
                    docker build -t ci-cd-devsecops-app:1.0 .
                '''
            }
        }

        stage('Docker Push') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'dockerhub-credentials',
                        usernameVariable: 'DOCKER_USERNAME',
                        passwordVariable: 'DOCKER_PASSWORD'
                    )
                ]) {
                    sh '''
                        echo "$DOCKER_PASSWORD" | docker login -u "$DOCKER_USERNAME" --password-stdin
                        docker tag ci-cd-devsecops-app:1.0 $DOCKER_USERNAME/ci-cd-devsecops-app:1.0
                        docker push $DOCKER_USERNAME/ci-cd-devsecops-app:1.0
                        docker logout
                    '''
                }
            }
        }
    }
}
