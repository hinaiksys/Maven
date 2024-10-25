pipeline {
    agent any

   
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
