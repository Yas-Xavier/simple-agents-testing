pipeline {
    agent any
    
    environment {
        REPO_URL = 'https://github.com/Yas-Xavier/simple-agents-testing.git'
        WEB_SERVER = '54.234.50.56'
        SSH_CREDENTIAL_ID = 'ssh-credentials'
    }
    
    stages {
        stage('Checkout Code') {
            steps {
                echo 'Checking out code from GitHub...'
                git branch: 'main', url: "${REPO_URL}"
            }
        }

        stage('Deploy Website') {
            steps {
                echo 'Deploying code to web server...'
                script {
                    withCredentials([sshUserPrivateKey(credentialsId: env.SSH_CREDENTIAL_ID, keyFileVariable: 'SSH_KEY')]) {
                        sh """
                            ssh -i \$SSH_KEY -o StrictHostKeyChecking=no ec2-user@${WEB_SERVER} '
                                sudo rm -rf /var/www/html/*
                                sudo git clone ${REPO_URL} /tmp/repo-temp
                                sudo cp -r /tmp/repo-temp/* /var/www/html/
                                sudo rm -rf /tmp/repo-temp
                                sudo chown -R apache:apache /var/www/html
                                sudo systemctl restart httpd
                            '
                        """
                    }
                }
            }
        }
    }

    post {
        success {
            echo '✅ Deployment completed successfully on dynamic agent.'
        }
        failure {
            echo '❌ Deployment failed. Please check logs.'
        }
    }
}
