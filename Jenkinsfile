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

                // Determine the artifact name based on whether it's a PR
                script {
                    def prStatus = (env.CHANGE_ID) ? "Raised" : "Modified"
                    def prId = env.CHANGE_ID ? env.CHANGE_ID : "noPR"
                    def artifactName = "PR#${prId} ${prStatus} | ${COMMIT_HASH}.jar"
                    echo "Archiving ${artifactName}"
                    
                    // Rename the jar with PR info and commit hash
                    sh "mv target/AWSCodeDeployDemo-0.0.1-SNAPSHOT.jar target/${artifactName}"
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
            echo 'Build failed. Attempting to rollback to the last successful build...'
            script {
                // Get the last successful build number
                def lastSuccessfulBuild = currentBuild.getPreviousSuccessfulBuild()
                if (lastSuccessfulBuild) {
                    echo "Rolling back to build #${lastSuccessfulBuild.number}..."
                    
                    // Assuming the artifact is in the same directory structure as the current build
                    def artifactName = lastSuccessfulBuild.getArtifacts()[0].getFileName() // Get the artifact name of the last successful build
                    sh "cp -f ${lastSuccessfulBuild.getArtifactsDir()}/${artifactName} target/${artifactName}" // Restore artifact
                    echo "Rollback to last successful build completed."
                } else {
                    echo "No previous successful build found. Cannot perform rollback."
                }
            }
        }
    }
}
