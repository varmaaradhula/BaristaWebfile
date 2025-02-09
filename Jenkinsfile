pipeline {
    agent any
    environment {
        SSH_USER = 'ubuntu' // Replace with your instance's username
        SSH_HOST = '18.133.120.114' // Replace with the AWS instance's public IP
        //SSH_KEY = 'instancekey.pem' // Path to the private SSH key in Jenkins
        REMOTE_DIR = '/var/www/html/' // Apache's default web directory
    }
    stages {

        stage('Fetch web code fron Git'){

            steps{
                echo 'Cloning webfiles repo'
                git branch: 'master', url: 'https://github.com/varmaaradhula/BaristaWebfile.git'
            }
        }

        stage('change mode of the key'){

            steps{
                echo 'Changing mode of the file'
                sh """
                chmod 400 instancekey

                """
            }
        }
        stage('Install HTTPD') {
            steps {
                script {
                    echo 'Installing HTTPD on AWS instance...'
                    sh """
                    ssh -o StrictHostKeyChecking=no -i ./instancekey ${SSH_USER}@${SSH_HOST} \
                    "sudo apt install -y apache2 && sudo systemctl start apache2 && sudo systemctl enable apache2"
                    """
                }
            }
        }
        stage('Copy Web Files') {
            steps {
                script {
                    echo 'Copying web files to AWS instance...'
                    sh """
                    scp -o StrictHostKeyChecking=no -i ./instancekey -r ./webfiles/* ${SSH_USER}@${SSH_HOST}:${REMOTE_DIR}
                    """
                }
            }
        }
        stage('Verify Deployment') {
            steps {
                script {
                    echo 'Verifying deployment...'
                    sh """
                    ssh -o StrictHostKeyChecking=no -i ./instancekey ${SSH_USER}@${SSH_HOST} \
                    "curl -I http://localhost:80"
                    """
                }
            }
        }
    }
    post {
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed. Check the logs for errors.'
        }
    }
}
