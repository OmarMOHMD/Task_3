pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                sh 'docker build -t omardevmedock/myapp .'
            }
        }

        stage('Push') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'omar-id', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                    sh 'docker login -u $DOCKER_USERNAME -p $DOCKER_PASSWORD'
                    sh 'docker push omardevmedock/myapp'
                }
            }
        }

        stage('Deploy') {
            steps {
                sh 'docker pull omardevmedock/myapp'
                sh 'docker run -d -p 80:80 omardevmedock/myapp'
            }
        }
    }

    post {
        always {
            slackSend (
                channel: '#jenkins-notifications',
                color: currentBuild.result == 'SUCCESS' ? 'good' : 'danger',
                message: "Pipeline finished: ${currentBuild.result}",
                tokenCredentialId: 'slack-bot-token'
            )
        }
    }
}
