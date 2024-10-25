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
                    def artifactName = "PR#${env.CHANGE_ID} ${prStatus} | ${COMMIT_HASH}.jar"
                    echo "Archiving ${artifactName}"
                    sh "mv target/*.jar target/${artifactName}" // Rename jar with PR info and commit hash
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
