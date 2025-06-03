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
                        if [ ! -d /opt/chat-app ]; then
                            git clone https://github.com/wbrymo/Node.js-Real-Time-Chat-App-.git /opt/chat-app;
                        fi
                        cd /opt/chat-app &&
                        git pull origin main &&
                        npm install &&
                        pm2 start app.js --name chat-app || pm2 restart chat-app
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
