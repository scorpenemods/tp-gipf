pipeline {
    agent any


    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Compilation') {
            steps {
                sh './gradlew -DhttpsproxyHost=proxy1-rech -DhttpsproxyPort=3128 compileJava'
            }
        }

        stage('Tests + JaCoCo') {
            steps {
                sh './gradlew test jacocoTestReport'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                sh "./gradlew sonar \
                  -Dsonar.projectKey=tp-gipf \
                  -Dsonar.projectName='tp-gipf' \
                  -Dsonar.host.url=http://172.17.0.1:9000 \
                  -Dsonar.token=sqp_aedac4cd369479f1b1412148fd67bf3ddb793cff"
            }
        }

        stage('Build JAR') {
            steps {
                sh './gradlew jar'
            }
        }

        stage('Archive JAR') {
            steps {
                archiveArtifacts artifacts: 'build/libs/*.jar', fingerprint: true
            }
        }
    }

    post {
        success {
            echo '✅'
        }
        failure {
            echo '❌'
        }
    }
}
