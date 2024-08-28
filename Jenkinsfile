pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                // Build the Docker image
                sh 'docker build -t myapp .'
            }
        }

        stage('Push') {
            steps {
                // Login to Docker Hub and push the image
                withCredentials([usernamePassword(credentialsId: 'omar-id', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                    sh 'docker login -u $DOCKER_USERNAME -p $DOCKER_PASSWORD'
                    sh 'docker push myapp'
                }
            }
        }

        stage('Deploy') {
            steps {
                // Pull the image and deploy it
                sh 'docker pull myapp'
                sh 'docker run -d -p 80:80 myapp'
            }
        }
    }

    post {
        always {
            // Send a Slack notification with the status of the pipeline
            slackSend (
                channel: '#jenkins-notifications',
                color: currentBuild.result == 'SUCCESS' ? 'good' : 'danger',
                message: "Pipeline ${currentBuild.fullDisplayName} finished with status: ${currentBuild.result}",
                tokenCredentialId: 'slack-bot-token'
            )
        }
    }
}
