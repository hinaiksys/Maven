pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                echo 'Starting Checkout stage...'
                
                // Check if we're in a pull request build context
                script {
                    if (env.CHANGE_ID) {
                        echo "This is a pull request build."
                        // Checkout the branch associated with the pull request
                        git url: 'https://github.com/hinaiksys/Maven.git', branch: env.CHANGE_BRANCH
                    } else {
                        echo "This is not a pull request build. Checking out the main branch."
                        git url: 'https://github.com/hinaiksys/Maven.git', branch: 'main'
                    }
                }

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
            echo 'Build failed. Attempting rollback to last successful build artifacts...'
            script {
                // Check for last successful build artifacts
                def lastSuccessful = currentBuild.rawBuild.getPreviousSuccessfulBuild()
                if (lastSuccessful != null) {
                    echo "Rolling back to artifacts from Build #${lastSuccessful.number}"
                    // Copy artifacts from the last successful build
                    copyArtifacts projectName: "${env.JOB_NAME}", selector: specific("${lastSuccessful.number}")

                    // Optionally, redeploy the last successful artifact if needed
                    // e.g., custom deploy command can be placed here
                    sh "./deploy.sh ${lastSuccessful.number}" // Replace with actual deploy script if applicable
                } else {
                    echo "No previous successful build to roll back to."
                }
            }
        }
        always {
            echo 'Cleaning up workspace...'
            cleanWs()
        }
    }
}
