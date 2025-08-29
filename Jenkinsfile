pipeline {
    agent any

    environment {
        AWS_HOST = "ubuntu@3.81.148.200"
        AZURE_HOST = "azureuser@52.191.78.102"
        SSH_KEY_AWS = credentials('vm-ssh-key')   // Jenkins SSH private key credential
        SSH_KEY_AZURE = credentials('vm-ssh-key-azure')
    }


    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/Aruun/capstone_repository.git'
            }
        }

        stage('Deploy to AWS VM') {
            steps {
                sshagent(['vm-ssh-key']) {
                    sh """
                        scp -o StrictHostKeyChecking=no index-aws.html $AWS_HOST:/tmp/index.html
                        ssh -o StrictHostKeyChecking=no $AWS_HOST 'sudo mv /tmp/index.html /var/www/html/index-aws.html && sudo systemctl restart nginx'
                    """
                }
            }
        }

        stage('Deploy to Azure VM') {
            steps {
                sshagent(['vm-ssh-key-azure']) {
                    sh """
                        scp -o StrictHostKeyChecking=no index-azure.html $AZURE_HOST:/tmp/index.html
                        ssh -o StrictHostKeyChecking=no $AZURE_HOST 'sudo mv /tmp/index.html /var/www/html/index-azure.html && sudo systemctl restart nginx'
                    """
                }
            }
        }
    }

    post {
        success {
            echo 'Deployment completed successfully'
        }
        failure {
            echo 'Deployment failed'
        }
    }
}
