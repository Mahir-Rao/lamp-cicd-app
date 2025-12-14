pipeline {
    agent { label 'ec2' }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/Mahir-Rao/lamp-cicd-app.git'
            }
        }

        stage('Deploy to LAMP Server') {
            steps {
                sshagent(['lamp-deploy-ssh']) {
                    sh '''
                    rsync -avz --delete ./ /var/www/html/app/
                    '''
                }
            }
        }
    }
}
