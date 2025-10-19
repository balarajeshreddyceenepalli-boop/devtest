pipeline {
    agent any

    environment {
        PATH = "/Users/rajesh/.nvm/versions/node/v18.20.8/bin:$PATH"
        // Supabase environment variables
        VITE_SUPABASE_URL = credentials('VITE_SUPABASE_URL')   // Jenkins credential ID
        VITE_SUPABASE_ANON_KEY = credentials('VITE_SUPABASE_ANON_KEY') // Jenkins credential ID
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

        stage('Clean & Build') {
            steps {
                sh '''
                    echo "Cleaning old build..."
                    rm -rf dist
                    echo "Building React app with Supabase env variables..."
                    npm run build
                '''
            }
        }

        stage('SonarQube Analysis') {
            environment {
                // Optional: SonarQube env
            }
            steps {
                withSonarQubeEnv('MySonarQube') {
                    sh '''
                        npx sonar-scanner \
                        -Dsonar.projectKey=devtest \
                        -Dsonar.sources=src \
                        -Dsonar.host.url=http://localhost:9000 \
                        -Dsonar.login=$SONAR_AUTH_TOKEN
                    '''
                }
            }
        }

        stage('Serve Application') {
            steps {
                sh '''
                    echo "Stopping any existing app..."
                    pkill -f 'serve -s dist' || true
                    echo "Starting app..."
                    nohup serve -s dist -l 3000 > serve.log 2>&1 &
                '''
                echo "✅ Application is live at http://localhost:3000"
                sh 'open http://localhost:3000'
            }
        }
    }

    post {
        success {
            echo "🎉 Build, Scan & Serve Successful!"
        }
        failure {
            echo "❌ Build failed. Please check logs."
        }
    }
}
