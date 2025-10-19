pipeline {
    agent any

    environment {
        // Use your system Node path
        PATH = "/Users/rajesh/.nvm/versions/node/v18.20.8/bin:$PATH"
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/balarajeshreddyceenepalli-boop/devtest.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'node -v'
                sh 'npm -v'
                sh 'npm install'
            }
        }

        stage('Build') {
            steps {
                sh 'npm run build'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('MySonarQube') {   // Jenkins -> Configure System -> SonarQube servers
                    sh '''
                        npx sonar-scanner \
                        -Dsonar.projectKey=devtest \
                        -Dsonar.sources=src \
                        -Dsonar.host.url=http://localhost:9000 \
                        -Dsonar.login=YOUR_SONAR_TOKEN
                    '''
                }
            }
        }

        stage('Serve Application') {
            steps {
                // Install "serve" globally (only runs if not installed)
                sh 'npm install -g serve || true'

                // Kill any existing serve process to avoid port conflicts
                sh "pkill -f 'serve -s dist' || true"

                // Start app in background on port 3000
                sh 'nohup serve -s dist -l 3000 > serve.log 2>&1 &'

                echo "✅ Application is live at http://localhost:3000"
            }
        }
    }

    post {
        success {
            echo "🎉 Build & Deploy Successful!"
            // Auto-open browser (Mac only)
            sh 'open http://localhost:3000'
        }
        failure {
            echo "❌ Build failed. Please check logs."
        }
    }
}
