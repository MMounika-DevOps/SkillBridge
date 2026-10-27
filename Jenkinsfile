pipeline {
    agent any

    options {
        timestamps()
    }

    environment {
        DEPLOY_ENV = '/home/ec2-user/projects/SkillBridge/.env'
        COMPOSE_PROJECT_NAME = 'skillbridge'
    }

    stages {
        stage('Checkout') {
            steps {
                echo 'SkillBridge source code is checked out from GitHub.'
            }
        }

        stage('Verify Docker') {
            steps {
                sh 'docker --version'
                sh 'docker compose version'
                sh 'docker ps --filter "name=skillbridge"'
            }
        }

        stage('Verify Environment') {
            steps {
                sh '''
                    test -r "$DEPLOY_ENV"
                    echo "Environment file is accessible."
                '''
            }
        }

        stage('Build Images') {
            steps {
                sh '''
                    docker compose \
                      --project-name "$COMPOSE_PROJECT_NAME" \
                      --env-file "$DEPLOY_ENV" \
                      build
                '''
            }
        }

        stage('Deploy Application') {
            steps {
                sh '''
                    docker compose \
                      --project-name "$COMPOSE_PROJECT_NAME" \
                      --env-file "$DEPLOY_ENV" \
                      up -d
                '''
            }
        }

        stage('Verify Containers') {
            steps {
                sh '''
                    docker compose \
                      --project-name "$COMPOSE_PROJECT_NAME" \
                      --env-file "$DEPLOY_ENV" \
                      ps
                '''
            }
        }

        stage('Application Test') {
            steps {
                sh '''
                    curl --fail --retry 5 --retry-delay 3 \
                      http://localhost/api/

                    curl --fail --retry 5 --retry-delay 3 \
                      http://localhost/api/db-test
                '''
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
