pipeline {
    agent any
 
    environment {
        SCANNER_HOME = tool 'sonarqube-scanner'
    }
 
    stages {
 
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/subasinik-blip/ultimate-devops-project-aws.git'
            }
        }
 
        stage('Build') {
            steps {
                dir('section-1/frontend') {
                    sh '''
                        npm install
                        npm run build
                    '''
                }
            }
        }
 
        stage('Test') {
            steps {
                dir('section-1/frontend') {
                    sh '''
                        echo "Running tests..."
                        npm test || true
                    '''
                }
            }
        }
 
        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('sonarqube') {
                    sh '''
                        $SCANNER_HOME/bin/sonar-scanner \
                        -Dsonar.projectKey=frontend-app \
                        -Dsonar.sources=section-1/frontend \
                        -Dsonar.host.url=http://<SONAR-IP>:9000 \
                        -Dsonar.login=<SONAR-TOKEN>
                    '''
                }
            }
        }
 
        stage('Quality Gate') {
            steps {
                timeout(time: 2, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: false
                }
            }
        }
 
        stage('Package') {
            steps {
                echo 'Frontend packaged (dist folder ready)'
            }
        }
 
        stage('Deploy') {
            steps {
                sh '''
                    sudo rm -rf /var/www/html/*
                    sudo cp -r section-1/frontend/dist/* /var/www/html/
                    sudo systemctl restart nginx
                '''
            }
        }
    }
}
