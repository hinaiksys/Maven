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

                // Archive the artifact with the commit hash and pull request ID in the name
                script {
                    def prStatus = env.CHANGE_ID ? "Raised" : "Modified"
                    def prId = env.CHANGE_ID ?: "noPR"
                    def artifactName = "PR#${prId} ${prStatus} | ${COMMIT_HASH}.jar"
                    echo "Archiving ${artifactName}"

                    // Rename the jar with the artifact name
                    sh "mv target/*.jar target/${artifactName}"
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
            echo 'Build failed. Attempting to roll back to the last successful build...'
            script {
                // Get the last successful build
                def lastSuccessfulBuild = currentBuild.getPreviousSuccessfulBuild()
                if (lastSuccessfulBuild) {
                    echo "Rolling back to build #${lastSuccessfulBuild.number}..."
                    
                    // Assume the artifact from the last successful build is located in its artifacts directory
                    def artifactPath = "${lastSuccessfulBuild.artifactsDir}/target"
                    sh """
                        mkdir -p target
                        cp -f ${artifactPath}/*.jar target/
                    """
                    echo "Rollback to the last successful build's artifacts completed."
                } else {
                    echo "No previous successful build found. Rollback is not possible."
                }
            }
        }
    }
}
