pipeline {
    agent { label 'workstation' }

    stages {
        stage('CI') {
            when {
                expression { env.BRANCH_NAME != env.TAG_NAME }
            }
            steps {
                sh "echo 'CI pipeline running...'"
            }
            post {
                success {
                    echo "CI stage completed successfully!"
                }
                failure {
                    echo "CI stage failed! Notifying team..."
                    sh "curl -X POST -H 'Content-Type: application/json' --data '{\"message\": \"CI stage failed!\"}' http://your-slack-webhook-url"
                }
                always {
                    echo "CI stage finished execution."
                }
            }
        }

        stage('CD') {
            when {
                expression { env.BRANCH_NAME != env.TAG_NAME }
            }
            steps {
                sh "echo 'CD pipeline running...'"
            }
            post {
                success {
                    echo "CD stage completed successfully!"
                }
                failure {
                    echo "CD stage failed! Sending alert..."
                }
            }
        }

        stage('Release') {
            when {
                expression { env.BRANCH_NAME == env.TAG_NAME }
            }
            steps {
                sh "echo 'Release pipeline running...'"
            }
            post {
                success {
                    echo "Release stage completed successfully! Deploying artifacts..."
                }
                failure {
                    echo "Release stage failed! Investigating..."
                }
            }
        }
    }

    post {
        always {
            echo "Pipeline execution finished."
        }
        success {
            echo "Pipeline completed successfully!"
        }
        failure {
            echo "Pipeline failed! Triggering rollback..."
        }
        unstable {
            echo "Pipeline is unstable! Some tests might have failed."
        }
    }
}
