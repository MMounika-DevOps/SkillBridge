
pipeline {
    agent any

    options {
        timestamps()
    }

    stages {
        stage('Checkout') {
            steps {
                echo 'Source code is checked out from GitHub.'
                checkout scm
            }
        }

        stage('Verify Docker') {
            steps {
                sh 'docker --version'
                sh 'docker compose version'
            }
        }

        stage('Build Images') {
            steps {
                sh 'docker compose build'
            }
        }

        stage('Deploy Application') {
            steps {
                sh 'docker compose up -d'
            }
        }

        stage('Verify Containers') {
            steps {
                sh 'docker compose ps'
                sh '''
                    docker inspect skillbridge_frontend \
                      --format="{{.State.Status}}"
                    docker inspect skillbridge_backend \
                      --format="{{.State.Status}}"
                    docker inspect skillbridge_db \
                      --format="{{.State.Status}}"
                '''
            }
        }

        stage('Application Test') {
            steps {
                sh 'curl --fail --retry 5 --retry-delay 3 http://localhost/api/'
                sh 'curl --fail --retry 5 --retry-delay 3 http://localhost/api/db-test'
            }
        }
    }

    post {
        success {
            echo 'SkillBridge CI/CD pipeline completed successfully.'
        }
        failure {
            echo 'Pipeline failed. Check the Jenkins Console Output.'
        }
    }
}
