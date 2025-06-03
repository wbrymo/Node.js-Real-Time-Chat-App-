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
                        # Ensure /opt/chat-app exists and is writable
                        sudo mkdir -p /opt/chat-app &&
                        sudo chown ec2-user:ec2-user /opt/chat-app

                        # Clone if missing, or pull latest
                        if [ ! -d /opt/chat-app/.git ]; then
                            git clone https://github.com/wbrymo/Node.js-Real-Time-Chat-App-.git /opt/chat-app;
                        else
                            cd /opt/chat-app && git pull origin main;
                        fi

                        cd /opt/chat-app &&
                        npm install

                        # Start or restart PM2 process
                        if pm2 describe chat-app > /dev/null; then
                            pm2 restart chat-app
                        else
                            pm2 start app.js --name chat-app
                        fi
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
