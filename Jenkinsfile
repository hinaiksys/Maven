pipeline {
    agent any 

    stages {
        stage('Checkout') {
            steps {
                checkout scm // Checkout the source code from the SCM
            }
        }
        stage('Build') {
            steps {
                // Change directory to where the pom.xml is located
                dir('Javarepo1') {
                    // Run Maven commands
                    bat 'mvn clean package' // Use 'sh' if running on a Unix-based system
                    // Optionally, display the Maven version
                    bat 'mvn --version' // Use 'sh' if running on a Unix-based system
                }
            }
        }
       
        stage('Archive Artifacts') {
            steps {
                // Archive the JAR file as an artifact
                archiveArtifacts artifacts: 'Javarepo1/target/*.jar', allowEmptyArchive: true
            }
        }
    }

    post {
        always {
            // Archive test results, if any
            junit '**/target/surefire-reports/*.xml' // Adjust this if your reports are in a different location
            // Clean up workspace if necessary
            cleanWs()
        }
        success {
            echo 'Pipeline succeeded!'
        }
        failure {
            echo 'Pipeline failed!'
        }
    }
}
