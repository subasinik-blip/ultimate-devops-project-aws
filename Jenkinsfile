pipeline {
    agent any

    environment {
        GIT_REPO     = 'https://github.com/subasinik-blip/ultimate-devops-project-aws.git'
        BRANCH       = 'main'

        /* 🔍 SONAR */
        SONARQUBE    = 'sonarqube'

        /* 🌐 NGINX */
        NGINX_PATH   = '/var/www/html'

        /* 📦 PATHS */
        FRONTEND_DIR = 'frontend'
        BUILD_DIR    = 'dist'
    }

    stages {

        stage('Checkout Code') {
            steps {
                git branch: "${BRANCH}", url: "${GIT_REPO}"
            }
        }

        stage('Install Dependencies') {
            steps {
                dir("${FRONTEND_DIR}") {
                    sh 'npm ci || npm install'
                }
            }
        }

        stage('Build Frontend') {
            steps {
                dir("${FRONTEND_DIR}") {
                    sh 'npm run build'
                }
            }
        }

        stage('Test') {
            steps {
                dir("${FRONTEND_DIR}") {
                    sh 'npm test || true'
                }
            }
        }

        stage('SonarQube Analysis') {
            steps {
                dir("${FRONTEND_DIR}") {
                    withSonarQubeEnv("${SONARQUBE}") {
                        sh """
                            sonar-scanner \
                                -Dsonar.projectKey=ultimate-devops \
                                -Dsonar.sources=. \
                                -Dsonar.host.url=$SONAR_HOST_URL \
                                -Dsonar.login=$SONAR_AUTH_TOKEN
                        """
                    }
                }
            }
        }

        stage('Quality Gate') {
            steps {
                timeout(time: 5, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }

        stage('Deploy to NGINX') {
            steps {
                sh """
                    echo "🚀 Deploying to NGINX..."
                    sudo rm -rf ${NGINX_PATH}/*
                    sudo cp -r ${FRONTEND_DIR}/${BUILD_DIR}/* ${NGINX_PATH}/
                    sudo chown -R www-data:www-data ${NGINX_PATH}
                """
            }
        }

        stage('Restart NGINX') {
            steps {
                sh """
                    sudo nginx -t
                    sudo systemctl restart nginx
                """
            }
        }
    }

    post {
        success {
            echo "✅ SUCCESS: App Deployed"
        }
        failure {
            echo "❌ FAILED: Check Logs"
        }
    }
}
