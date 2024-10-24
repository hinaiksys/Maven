pipeline {
    agent any

    tools {
        maven 'Maven 3.6.3' // Ensure this version is configured in Jenkins
        jdk 'JDK 11' // Ensure this JDK version is configured in Jenkins
    }

    stages {
        stage('Checkout') {
            steps {
                git url: 'https://github.com/hinaiksys/Maven.git'
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('Archive Artifacts') {
            steps {
                archiveArtifacts artifacts: 'target/*.jar', fingerprint: true // Adjust the pattern as needed
            }
        }

        stage('Deliver') {
            steps {
                sh './deliver-script.sh' // Custom delivery script
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
