pipeline {
    agent { label 'ec2' }

    environment {
        LAMP_HOST = "13.232.189.54"
        LAMP_USER = "deploy"
        REMOTE_DIR = "/var/www/html/app"
        SSH_KEY = "/home/jenkins/.ssh/jenkins_deploy_key"
    }

    stages {

        stage('Deploy to LAMP Server') {
            steps {
                sh '''
                    set -e

                    ssh -i ${SSH_KEY} -o IdentitiesOnly=yes \
                        ${LAMP_USER}@${LAMP_HOST} "
                        sudo mkdir -p ${REMOTE_DIR} &&
                        sudo chown -R ${LAMP_USER}:www-data ${REMOTE_DIR} &&
                        sudo chmod -R 775 ${REMOTE_DIR}
                    "

                    rsync -avz --delete \
                      --exclude='.git' \
                      --exclude='Jenkinsfile' \
                      -e "ssh -i ${SSH_KEY} -o IdentitiesOnly=yes" \
                      ./ ${LAMP_USER}@${LAMP_HOST}:${REMOTE_DIR}/

                    ssh -i ${SSH_KEY} -o IdentitiesOnly=yes \
                        ${LAMP_USER}@${LAMP_HOST} \
                        "sudo systemctl reload apache2"
                '''
            }
        }
    }
}

