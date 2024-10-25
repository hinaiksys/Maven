pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                echo 'Starting Checkout stage...'
                git branch: 'main', url: 'https://github.com/hinaiksys/Maven.git'
            }
        }

        stage('Build') {
            steps {
                echo 'Starting Build stage...'
                sh 'mvn clean package'
            }
        }

        stage('Archive Artifacts') {
            steps {
                echo 'Starting Archive Artifacts stage...'
                archiveArtifacts artifacts: 'target/*.jar', fingerprint: true // Adjust the pattern as needed
            }
        }
    }

    post {
        success {
            echo 'Build completed successfully!'
        }
        failure {
            echo 'Build failed.'
        }
    }
}
