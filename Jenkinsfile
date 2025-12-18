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

        stage('Tests + JaCoCo') {
            steps {
                sh './gradlew test jacocoTestReport'
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
            echo '✅ Pipeline avec JaCoCo terminé avec succès'
        }
        failure {
            echo '❌ Pipeline échoué'
        }
    }
}
