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
                sh './gradlew compileJava'
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
