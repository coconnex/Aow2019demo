pipeline {
    agent any

    environment {
        DEPLOY_DIR = '/usr/local/apache/htdocs/gittest'  // Deployment directory on your Amazon Linux 2 server
        GIT_REPO = 'git@github.com:coconnex/drupaldemo.git'  // Your GitHub repository URL
        SERVER = 'ec2-user@52.56.45.90'  // SSH username for your Amazon Linux 2 server
    }

    stages {
        stage('Checkout Code') {
            steps {
                script {
                    // Pull the latest code from the GitHub repository
                    git url: "${GIT_REPO}", branch: 'main'  // Ensure you're using the correct branch (e.g., 'main')
                }
            }
        }

        stage('Install Dependencies') {
            steps {
                script {
                    // Install Apache and PHP on Amazon Linux 2 using yum
                    echo 'Installing Apache and PHP dependencies...'
                    sh 'sudo yum update -y'   // Update system
                    sh 'sudo yum install -y httpd php php-cli php-mysqlnd php-fpm php-xml'  // Install Apache and PHP
                }
            }
        }

        stage('Deploy Code to Server') {
            steps {
                script {
                    // Deploy the code to the target directory on your Amazon Linux 2 server using rsync
                    echo 'Deploying code to the server...'
                    sh "rsync -avz --exclude='.git' . ${SERVER}:${DEPLOY_DIR}"  // Rsync from Jenkins workspace to server
                }
            }
        }

        stage('Restart Apache') {
            steps {
                script {
                    // Restart Apache to apply the new changes
                    echo 'Restarting Apache on the server...'
                    sh "ssh ${SERVER} 'sudo systemctl restart httpd'"  // Restart Apache to reflect code changes
                }
            }
        }
    }

    post {
        success {
            echo 'Deployment successful!'
        }
        failure {
            echo 'Deployment failed!'
        }
    }
}
