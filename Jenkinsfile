pipeline {
    agent any

    environment {
        GIT_REPO = 'https://github.com/subasinik-blip/ultimate-devops-project-aws.git'
        BRANCH = 'main'

        /* 🔍 SONAR */
        SONARQUBE = 'sonarqube'

        /* 🌐 NGINX */
        NGINX_PATH = '/var/www/html'

        /* 📦 PATHS (adjust if needed) */
        FRONTEND_DIR = 'frontend'
        BACKEND_DIR = 'backend'
        BUILD_DIR = 'dist'
    }

    stages {

        /* ========== 1️⃣ CHECKOUT ========== */
        stage('Checkout Code') {
            steps {
                git branch: "${BRANCH}", url: "${GIT_REPO}"
            }
        }

        /* ========== 2️⃣ INSTALL DEPENDENCIES ========== */
        stage('Install Dependencies') {
            steps {
                dir("${FRONTEND_DIR}") {
                    sh 'npm ci || npm install'
                }
            }
        }

        /* ========== 3️⃣ BUILD ========== */
        stage('Build Frontend') {
            steps {
                dir("${FRONTEND_DIR}") {
                    sh 'npm run build'
                }
            }
        }

        /* ========== 4️⃣ TEST ========== */
        stage('Test') {
            steps {
                dir("${FRONTEND_DIR}") {
                    sh 'npm test || true'
                }
            }
        }

        /* ========== 5️⃣ SONARQUBE ========== */
        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv("${SONARQUBE}") {
                    sh '''
                    npm install -g sonar-scanner

                    sonar-scanner \
                      -Dsonar.projectKey=ultimate-devops \
                      -Dsonar.sources=. \
                      -Dsonar.host.url=$SONAR_HOST_URL \
                      -Dsonar.login=$SONAR_AUTH_TOKEN
                    '''
                }
            }
        }

        /* ========== 6️⃣ QUALITY GATE ========== */
        stage('Quality Gate') {
            steps {
                timeout(time: 5, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }

        /* ========== 7️⃣ DEPLOY FRONTEND (NGINX) ========== */
        stage('Deploy to NGINX') {
            steps {
                sh '''
                echo "🚀 Deploying to NGINX..."
                sudo rm -rf /var/www/html/*
                sudo cp -r frontend/dist/* /var/www/html/
                sudo chown -R www-data:www-data /var/www/html
                '''
            }
        }

        /* ========== 8️⃣ RESTART NGINX ========== */
        stage('Restart NGINX') {
            steps {
                sh '''
                sudo nginx -t
                sudo systemctl restart nginx
                '''
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
