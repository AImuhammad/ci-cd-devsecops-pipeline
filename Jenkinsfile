pipeline {
    agent any

    stages {

        stage('Checkout') {
            steps {
                git(
                    branch: 'main',
                    credentialsId: 'github-token',
                    url: 'https://github.com/AImuhammad/ci-cd-devsecops-pipeline.git'
                )
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

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SonarQube') {
                    withCredentials([
                        string(
                            credentialsId: 'sonarqube-token',
                            variable: 'SONAR_TOKEN'
                        )
                    ]) {
                        script {
                            def scannerHome = tool 'SonarQubeScanner'

                            withEnv(["SCANNER_HOME=${scannerHome}"]) {
                                sh '''
                                    "$SCANNER_HOME/bin/sonar-scanner" \
                                        -Dsonar.projectKey=ci-cd-devsecops-pipeline \
                                        -Dsonar.sources=app \
                                        -Dsonar.tests=tests \
                                        -Dsonar.host.url="$SONAR_HOST_URL" \
                                        -Dsonar.token="$SONAR_TOKEN"
                                '''
                            }
                        }
                    }
                }
            }
        }

        stage('Quality Gate') {
            steps {
                timeout(time: 5, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }

        stage('Docker Build') {
            steps {
                sh '''
                    docker build \
                        -t ci-cd-devsecops-app:5.0 .
                '''
            }
        }

        stage('Trivy Security Scan') {
            steps {
                sh '''
                    trivy image \
                        --severity CRITICAL \
                        --exit-code 1 \
                        ci-cd-devsecops-app:5.0
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
                        echo "$DOCKER_PASSWORD" | \
                            docker login \
                            -u "$DOCKER_USERNAME" \
                            --password-stdin

                        docker tag \
                            ci-cd-devsecops-app:5.0 \
                            "$DOCKER_USERNAME/ci-cd-devsecops-app:5.0"

                        docker push \
                            "$DOCKER_USERNAME/ci-cd-devsecops-app:5.0"

                        docker logout
                    '''
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh '''
                    export KUBECONFIG=/var/jenkins_home/jenkins-k8s/config

                    kubectl apply -f k8s/deployment.yaml
                    kubectl apply -f k8s/service.yaml

                    kubectl rollout status \
                        deployment/ci-cd-devsecops-app \
                        --timeout=120s
                '''
            }
        }

        stage('Kubernetes Health Check') {
            steps {
                sh '''
                    export KUBECONFIG=/var/jenkins_home/jenkins-k8s/config

                    kubectl get pods
                    kubectl get deployment ci-cd-devsecops-app
                    kubectl get service ci-cd-devsecops-app
                '''
            }
        }
    }

    post {
        always {
            sh 'docker ps -a'
        }

        success {
            echo 'CI/CD DevSecOps pipeline completed successfully.'
        }

        failure {
            echo 'CI/CD DevSecOps pipeline failed.'
        }
    }
}
