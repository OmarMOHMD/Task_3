pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                sh 'docker build -t myapp .'
            }
        }

        stage('Push') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'omar-id', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                    sh 'docker login -u $DOCKER_USERNAME -p $DOCKER_PASSWORD'
                    sh 'docker push myapp'
                }
            }
        }

        stage('Deploy') {
            steps {
                sh 'docker pull myapp'
                sh 'docker run -d -p 80:80 myapp'
            }
        }
    }

    post {
        always {
            slackSend (
                channel: '#jenkins-notifications',
                color: 'good',
                message: "Pipeline finished: ${currentBuild.result}",
                tokenCredentialId: 'slack-bot-token'  // Use the new Slack token ID here
            )
        }
    }
}
