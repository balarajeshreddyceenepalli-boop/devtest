pipeline {
    agent any

    environment {
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
                sh 'npm install'
            }
        }

        stage('Build') {
            steps {
                // Inject Supabase credentials from Jenkins
                withCredentials([
                    string(credentialsId: 'SUPABASE_URL', variable: 'VITE_SUPABASE_URL'),
                    string(credentialsId: 'SUPABASE_KEY', variable: 'VITE_SUPABASE_ANON_KEY')
                ]) {
                    sh '''
                        echo "Building React app with Supabase env variables..."
                        npm run build
                    '''
                }
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('MySonarQube') {
                    sh '''
                        npx sonar-scanner \
                        -Dsonar.projectKey=devtest \
                        -Dsonar.sources=src \
                        -Dsonar.host.url=$SONAR_HOST_URL \
                        -Dsonar.login=$SONAR_AUTH_TOKEN
                    '''
                }
            }
        }

        stage('Serve Application') {
            steps {
                // Kill existing serve process to avoid port conflicts
                sh "pkill -f 'serve -s dist' || true"

                // Start app in background on port 3000
                sh 'nohup serve -s dist -l 3000 > serve.log 2>&1 &'

                echo "✅ Application is live at http://localhost:3000"
            }
        }
    }

    post {
        success {
            echo "🎉 Build, Scan & Serve Successful!"
            // Open app in browser (Mac only)
            sh 'open http://localhost:3000'
        }
        failure {
            echo "❌ Pipeline failed. Check logs."
        }
    }
}
