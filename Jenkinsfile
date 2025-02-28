pipeline {
    agent any
    stages {
        stage('Clone Repository') {
            steps {
                git 'https://github.com/Abdulk-P/E-com-django.git'
            }
        }
        stage('Build Docker Image') {
            steps {
                sh 'docker build -t my-django-app .'
            }
        }
        stage('Scan with Trivy') {
            steps {
                sh '''
                sudo apt-get update
                sudo apt-get install -y wget apt-transport-https gnupg lsb-release
                wget -qO - https://aquasecurity.github.io/trivy-repo/deb/public.key | sudo tee /etc/apt/trusted.gpg.d/trivy.asc > /dev/null
                echo "deb https://aquasecurity.github.io/trivy-repo/deb $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/trivy.list
                sudo apt-get update
                sudo apt-get install -y trivy
                trivy image my-django-app || true
                '''
            }
        }
        stage('Run Django Container') {
            steps {
                sh 'docker stop my-django-app || true'
                sh 'docker rm my-django-app || true'
                sh 'docker run -d -p 8000:8000 -v $PWD/db.sqlite3:/app/db.sqlite3 --name my-django-app my-django-app'
            }
        }
    }
}
