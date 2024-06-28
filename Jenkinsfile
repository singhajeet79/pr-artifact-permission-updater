pipeline {
    agent any

    triggers {
        githubPullRequest()
    }

    environment {
        ARTIFACTORY_URL = 'http://localhost:8082/artifactory'
        REPO_NAME = 'java-web-app'
        ARTIFACT_PATH = "java-web-app/target/demo-0.0.1-SNAPSHOT.jar"
    }

    stages {
        stage('Check PR Description') {
            steps {
                script {
                    // Extract PR description
                    def prDescription = ghprbPullDescription
                    echo "PR Description: ${prDescription}"

                    // Check if PR modifies permission for the user!
                    if (!prDescription.contains("modify permission")) {
                        error("PR does not contain 'modify permission' in the description.")
                    }
                }
            }
        }

        stage('Modify Permission') {
            steps {
                script {
                    // Example command to modify permissions using Artifactory REST API
                    sh """
                    curl -u e5081:pswdJan1! -X PUT "${ARTIFACTORY_URL}/api/security/permissions/${REPO_NAME}" -d '{
                        "principals": {
                            "users": {
                                "e5081": ["r"]
                            }
                        }
                    }'
                    """
                    echo "Permissions modified for the user."
                }
            }
        }

        stage('Test Download') {
            steps {
                script {
                    // Test if the user can download the artifact
                    sh """
                    curl -u e5081:pswdJan1! -O "${ARTIFACTORY_URL}/${ARTIFACT_PATH}"
                    """
                    echo "Artifact downloaded successfully."
                }
            }
        }
    }

    post {
        success {
            echo 'Pipeline succeeded!'
        }
        failure {
            echo 'Pipeline failed.'
        }
    }
}
