pipeline {
    agent any
 
    stages {
 
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/subasinik-blip/ultimate-devops-project-aws.git'
            }
        }
 
        stage('Build') {
            steps {
                dir('section-1') {
                    echo 'Building project...'
                    sleep 5
                }
            }
        }
 
        stage('Test') {
            steps {
                dir('section-1') {
                    echo 'Running tests...'
                    sleep 5
                }
            }
        }
 
        stage('SonarQube Analysis') {
            steps {
                echo 'Running SonarQube...'
                sleep 5
            }
        }
 
        stage('Package') {
            steps {
                dir('section-1') {
                    echo 'Packaging...'
                    sleep 5
                }
            }
        }
 
        stage('Deploy') {
            steps {
                echo 'Deploying...'
                sleep 5
            }
        }
    }
}
