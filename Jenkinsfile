pipeline {
    agent { label 'ec2' }

    environment {
        LAMP_HOST = "13.232.189.54"
        LAMP_USER = "deploy"
        REMOTE_DIR = "/var/www/html/app"
        SSH_CRED  = "lamp-deploy-ssh"
    }

    stages {

        stage('Checkout Code') {
            steps {
                // Pipeline-from-SCM already checks out code,
                // but keeping this explicit is fine and clear
                git branch: 'main',
                    url: 'https://github.com/Mahir-Rao/lamp-cicd-app.git'
            }
        }

        stage('Deploy to LAMP Server') {
            steps {
                sshagent(credentials: [env.SSH_CRED]) {
                    sh '''
                        set -e

                        # Ensure target directory exists and is writable
                        ssh ${LAMP_USER}@${LAMP_HOST} "
                            sudo mkdir -p ${REMOTE_DIR} &&
                            sudo chown -R ${LAMP_USER}:www-data ${REMOTE_DIR} &&
                            sudo chmod -R 775 ${REMOTE_DIR}
                        "

                        # Sync website files to LAMP server
                        rsync -avz --delete \
                          --exclude='.git' \
                          --exclude='Jenkinsfile' \
                          ./ ${LAMP_USER}@${LAMP_HOST}:${REMOTE_DIR}/

                        # Reload Apache to reflect changes
                        ssh ${LAMP_USER}@${LAMP_HOST} "sudo systemctl reload apache2"
                    '''
                }
            }
        }
    }
}

