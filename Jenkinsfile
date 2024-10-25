pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                echo 'Starting Checkout stage...'
                git branch: 'main', url: 'https://github.com/hinaiksys/Maven.git'

                // Get the short commit hash
                script {
                    COMMIT_HASH = sh(script: "git rev-parse --short=6 HEAD", returnStdout: true).trim()
                    echo "Commit Hash: ${COMMIT_HASH}"
                }
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

                // Archive the artifact with the commit hash in the name
                script {
                    def artifactName = "maven-${COMMIT_HASH}.jar"
                    echo "Archiving ${artifactName}"
                    sh "mv target/*.jar target/${artifactName}" // Rename jar with commit hash
                    archiveArtifacts artifacts: "target/${artifactName}", fingerprint: true
                }
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
