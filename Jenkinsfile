
pipeline {
    agent any

    stages {
        stage('Clone Repo') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/main']],
                    userRemoteConfigs: [[url: 'https://github.com/wbrymo/Node.js-Real-Time-Chat-App-.git']]])
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }

        stage('Deploy to EC2') {
            steps {
                sshagent(['ec2-ssh-key']) {
                    sh '''
                        ssh -o StrictHostKeyChecking=no ec2-user@35.175.80.176 "
                        cd /opt/chat-app &&
                        git pull &&
                        npm install &&
                        pm2 restart chat-app || pm2 start app.js --name chat-app
                        "
                    '''
                }
            }
        }
    }

    post {
        always {
            cleanWs()
            echo '🚀 Chat app deployed. Access it via the Elastic IP!'
        }
    }
}
