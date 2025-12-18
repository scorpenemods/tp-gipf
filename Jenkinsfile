pipeline {
    agent any
    
    environment {
        DOCKER_IMAGE = "jenkins-demo-app"
        DOCKER_TAG = "${BUILD_NUMBER}"
        CONTAINER_NAME = "jenkins-demo-container"

        SCANNER_HOME = tool 'SonarQube-Server'
    }
    
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
    

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SonarQube') {
                    sh """
                        ${SCANNER_HOME}/bin/sonar-scanner \
                        -Dsonar.projectKey=nodejs-jenkins-sample-app \
                        -Dsonar.projectName=nodejs-sample-app \
                        -Dsonar.sources=. \
                        -Dsonar.exclusions=**/node_modules/**,**/test/**
                    """
                }
            }
        }

        stage("Quality Gate") {
            steps {
                timeout(time: 2, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }
        
        stage('Build Docker Image') {
            steps {
                script {
                    docker.build("${DOCKER_IMAGE}:${DOCKER_TAG}")
                }
            }
        }
        
        stage('Deploy') {
            steps {
                script {
                    def fullImage = "${DOCKER_IMAGE}:${BUILD_NUMBER}"
                    
                    sh """
                        docker rm -f ${CONTAINER_NAME} || true
                        docker ps -a --filter "publish=3000" -q | xargs -r docker rm -f || true
                        
                        docker run -d \
                            --name ${CONTAINER_NAME} \
                            -p 3000:3000 \
                            ${fullImage}
                    """
                }
            }
        }
    }
    
    post {
        always {
            sh "docker image prune -f"
        }
        success {
            echo 'Pipeline Success! Application deployed.'
        }
        failure {
            echo 'Pipeline Failed. Check logs.'
        }
    }
}
